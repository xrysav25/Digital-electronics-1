## LAB 7
# 1. Preparation task
| **Input P** | `0` | `0` | `1` | `1` | `0` | `1` | `0` | `1` | `1` | `1` | `1` | `0` | `0` | `1` | `1` | `1` |
| :-- | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| **Clock** | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) | ![rising](eq_uparrow.png) |
| **State** | A | A | B | C | C | D | A | B | C | D | B | B | B | C | D | B |
| **Output R** | `0` | `0` | `0` | `0` | `0` | `1` | `0` | `0` | `0` | `1` | `0` | `0` | `0` | `0` | `1` | `0` |

![deska](board.png)

| **RGB LED** | **Artix-7 pin names** | **Red** | **Yellow** | **Green** |
| :-: | :-: | :-: | :-: | :-: |
| LD16 | N15, M16, R12 | `1,0,0` | `1,1,0` | `0,1,0` |
| LD17 | N16, R11, G14 | `1,0,0` | `1,1,0` | `0,1,0` |

# 2. Traffic light controller
* State diagram
![diagram](diagram1.png)
* p_traffic_fsm
```vhdl
p_traffic_fsm : process(clk)
begin
    if rising_edge(clk) then
        if (reset = '1') then       -- Synchronous reset
            s_state <= STOP1 ;      -- Set initial state
            s_cnt   <= c_ZERO;      -- Clear all bits

        elsif (s_en = '1') then
            -- Every 250 ms, CASE checks the value of the s_state 
            -- variable and changes to the next state according 
            -- to the delay value.
            case s_state is

                -- If the current state is STOP1, then wait 1 sec
                -- and move to the next GO_WAIT state.
                when STOP1 =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_1SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= WEST_GO;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;

                when WEST_GO =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_4SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= WEST_WAIT;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;
                when WEST_WAIT =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_2SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= STOP2;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;
                when STOP2 =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_1SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= SOUTH_GO;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;
                when SOUTH_GO =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_4SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= SOUTH_WAIT;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;
                when SOUTH_WAIT =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_4SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= STOP1;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;


                    -- WRITE YOUR CODE HERE


                -- It is a good programming practice to use the 
                -- OTHERS clause, even if all CASE choices have 
                -- been made. 
                when others =>
                    s_state <= STOP1;

            end case;
        end if; -- Synchronous reset
    end if; -- Rising edge
end process p_traffic_fsm;
```
* p_output_fsm
```vhdl
p_output_fsm : process(s_state)
begin
    case s_state is
        when STOP1 =>
            south_o <= c_RED;
            west_o  <= c_RED;
        when WEST_GO =>
            south_o <= c_RED;
            west_o  <= c_GREEN;
        when WEST_WAIT =>
            south_o <= c_RED;
            west_o  <= c_YELLOW;
        when STOP2 =>
            south_o <= c_RED;
            west_o  <= c_RED;
        when SOUTH_GO =>
            south_o <= c_GREEN;
            west_o  <= c_RED;
        when SOUTH_WAIT =>
            south_o <= c_YELLOW;
            west_o  <= c_RED;
        

            -- WRITE YOUR CODE HERE


        when others =>
            south_o <= c_RED;
            west_o  <= c_RED;
    end case;
end process p_output_fsm;
```
* simulation
![simulace](simulace1.png)

# 3. Smart controller
* State table
| States, in/out | west_pin | south_pin | west_o | south_o | next state |
| :-: | :-: | :-: | :-: | :-: | :-: |
| STOP1 | X | X | R | R | WEST_GO |
| WEST_GO | X | 0/1 | G | R | WEST_GO/WEST_WAIT |
| WEST_WAIT | X | X | Y | R | STOP2 |
| STOP2 | X | X | R | R | SOUTH_GO |
| SOUTH_GO | 0/1 | X | R | G | SOUTH_GO/SOUTH_WAIT |
| SOUTH_WAIT | X | X | R | Y | STOP1 |
* State diagram
![diagram](diagram1.png)
* p_smart_traffic_fsm
```vhdl
p_smart_traffic_fsm : process(clk)
begin
    if rising_edge(clk) then
        if (reset = '1') then       -- Synchronous reset
            s_state <= STOP1 ;      -- Set initial state
            s_cnt   <= c_ZERO;      -- Clear all bits

        elsif (s_en = '1') then
            -- Every 250 ms, CASE checks the value of the s_state 
            -- variable and changes to the next state according 
            -- to the delay value.
            case s_state is

                -- If the current state is STOP1, then wait 1 sec
                -- and move to the next GO_WAIT state.
                when STOP1 =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_1SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= WEST_GO;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;

                when WEST_GO =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_4SEC) then
                        s_cnt <= s_cnt + 1;
                    elseif (south_pin = '1')
                        -- Move to the next state
                        s_state <= WEST_WAIT;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;
                when WEST_WAIT =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_2SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= STOP2;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;
                when STOP2 =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_1SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= SOUTH_GO;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;
                when SOUTH_GO =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_4SEC) then
                        s_cnt <= s_cnt + 1;
                    elsif (west_pin = '1')
                        -- Move to the next state
                        s_state <= SOUTH_WAIT;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;
                when SOUTH_WAIT =>
                    -- Count up to c_DELAY_1SEC
                    if (s_cnt < c_DELAY_4SEC) then
                        s_cnt <= s_cnt + 1;
                    else
                        -- Move to the next state
                        s_state <= STOP1;
                        -- Reset local counter value
                        s_cnt   <= c_ZERO;
                    end if;


                    -- WRITE YOUR CODE HERE


                -- It is a good programming practice to use the 
                -- OTHERS clause, even if all CASE choices have 
                -- been made. 
                when others =>
                    s_state <= STOP1;

            end case;
        end if; -- Synchronous reset
    end if; -- Rising edge
end process p_smart_traffic_fsm;
```