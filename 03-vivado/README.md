## LAB 3
# 1. Connection with 16 switches and LEDs
   ![deska](board.png)
# 2. Multiplexor
   * VHDL architecture
   ```vhdl
   architecture Behavioral of mux_4_1 is

   begin
      f_o <= a_i when (sel_i = "00") else
             b_i when (sel_i = "01") else
             c_i when (sel_i = "10") else
             d_i;
  
   end Behavioral;
   ```
   * VHDL simuls proces
   ```vhdl
    p_stimulus : process
    begin
        s_d <= "00"; s_c <= "00"; s_b <= "00"; s_a <= "00"; s_sel <= "00"; wait for 50 ns;
        assert (s_f = "00") report "Test failed for sel input combination: 00" severity error;
        s_a <= "01"; wait for 50 ns;
        assert (s_f = "01") report "Test failed for sel input combination: 00" severity error;
        s_a <= "10"; wait for 50 ns;
        assert (s_f = "10") report "Test failed for sel input combination: 00" severity error;
        s_a <= "11"; wait for 50 ns;
        assert (s_f = "11") report "Test failed for sel input combination: 00" severity error;
        
        s_sel <= "01"; wait for 50 ns;
        assert (s_f = "00") report "Test failed for sel input combination: 01" severity error;
        s_b <= "01"; wait for 50 ns;
        assert (s_f = "01") report "Test failed for sel input combination: 01" severity error;
        s_b <= "10"; wait for 50 ns;
        assert (s_f = "10") report "Test failed for sel input combination: 01" severity error;
        s_b <= "11"; wait for 50 ns;
        assert (s_f = "11") report "Test failed for sel input combination: 01" severity error;
        
        s_sel <= "10"; wait for 50 ns;
        assert (s_f = "00") report "Test failed for sel input combination: 10" severity error;
        s_c <= "01"; wait for 50 ns;
        assert (s_f = "01") report "Test failed for sel input combination: 10" severity error;
        s_c <= "10"; wait for 50 ns;
        assert (s_f = "10") report "Test failed for sel input combination: 10" severity error;
        s_c <= "11"; wait for 50 ns;
        assert (s_f = "11") report "Test failed for sel input combination: 10" severity error;
        
        s_sel <= "11"; wait for 50 ns;
        assert (s_f = "00") report "Test failed for sel input combination: 11" severity error;
        s_d <= "01"; wait for 50 ns;
        assert (s_f = "01") report "Test failed for sel input combination: 11" severity error;
        s_d <= "10"; wait for 50 ns;
        assert (s_f = "10") report "Test failed for sel input combination: 11" severity error;
        s_d <= "11"; wait for 50 ns;
        assert (s_f = "11") report "Test failed for sel input combination: 11" severity error;
        
    end process p_stimulus;
    ```
    * Simulation timewaves
    ![simulace](simul.png)
# 3. Vivado tutorial

# Vivado New Project Tutorial
1. Create a new project
2. Use difolt type: RTL Poroject
   ![tutorial](tutorial_1.png)
3. Add source - create some designt file!
   ![tutorial](tutorial_2.png)
4. Add Constraints - those files are usefull for hw implementation. No need to create one when we are going to just simulate something
5. Default Part - choose your board!
6. Finish!
7. So here you have your new project. In this window you can add inputs and outputs.
   ![tutorial](tutorial_3.png)
8. Your new design file is right here
   ![tutorial](tutorial_4.png)
9. For playing, you need some test file as well. Create one!
   Find "Add Source" in Project Manager menu or right click on something in your source menu. Select "Add or create simulation source"
   ![tutorial](tutorial_5.png)
10. Go next and choose create file (if you don't have one somewhere hidden!) and name your file. It's better to use "tb" and name of your design file.
   ![tutorial](tutorial_6.png)
11. After compleeting testbench, you can run it! Find "Run Simulation" in Simulation menu or right click in Source menu on Simulation source