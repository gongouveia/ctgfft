library IEEE;
USE IEEE.STD_LOGIC_1164.ALL;
USE ieee.numeric_std.ALL;      -- going to use to convert std vector to unsigned


-- Package Declaration Section-0.02112+0.j
package Packages_Util is
    
    
    CONSTANT  rows :     INTEGER :=  3;
    CONSTANT  collumns : INTEGER :=  3;
    
    
    ----------------------------------------------------------
    ------              Clocks                         -------
    ----------------------------------------------------------
   
    
    --clk generation.For 100 MHz clock this generates 1 Hz clock.
    CONSTANT acquisition_clock_counter : INTEGER := 100000000;
    CONSTANT output_clock_counter      : INTEGER := 100000000;
 
 
    ----------------------------------------------------------
    ------             Data Types                      -------
    ----------------------------------------------------------
   
 
	TYPE Complex_Type IS
		RECORD
			r: signed(31 DOWNTO 0);
			i: signed(31 DOWNTO 0);
		END RECORD;
		
    TYPE VECTOR_COLLUMN IS ARRAY (0 to collumns-1) OF Complex_Type; 
    TYPE VECTOR_ROW     IS ARRAY (0 to rows-1)     OF Complex_Type; 
    
    TYPE MATRIX         IS ARRAY (0 TO rows-1) OF VECTOR_COLLUMN;
    TYPE MATRIX_transpose  IS ARRAY (0 TO collumns-1) OF VECTOR_ROW;

    TYPE ROW_MATRIX     IS ARRAY (0 TO rows-1) OF VECTOR_ROW;
    TYPE COLLUMN_MATRIX IS ARRAY (0 TO collumns-1) OF VECTOR_COLLUMN;

    ----------------------------------------------------------
    ------           Pre Computed Matrix               -------
    ----------------------------------------------------------
    -- os valores estão calculados em 2s complement " 
CONSTANT twiddle_matrix : MATRIX :=
 (((x"00001000",x"00000000"),(x"00001000",x"00000000"),(x"00001000",x"00000000")),
((x"00001000",x"00000000"),(x"00000c42",x"fffff5b7"),(x"000002c7",x"fffff03e")),
((x"00001000",x"00000000"),(x"000002c7",x"fffff03e"),(x"fffff0f7",x"fffffa87"))
);

CONSTANT collumn_dft_matrix_values : ROW_MATRIX :=
 (((x"00001000",x"00000000"),(x"00001000",x"00000000"),(x"00001000",x"00000000")),
((x"00001000",x"00000000"),(x"fffff800",x"fffff225"),(x"fffff800",x"00000ddb")),
((x"00001000",x"00000000"),(x"fffff800",x"00000ddb"),(x"fffff800",x"fffff225")));


CONSTANT row_dft_matrix_values : COLLUMN_MATRIX :=
 (((x"00001000",x"00000000"),(x"00001000",x"00000000"),(x"00001000",x"00000000")),
((x"00001000",x"00000000"),(x"fffff800",x"fffff225"),(x"fffff800",x"00000ddb")),
((x"00001000",x"00000000"),(x"fffff800",x"00000ddb"),(x"fffff800",x"fffff225")));
    -------------------------------------------------------
    ------        Deslaração de Funções             -------
    -------------------------------------------------------
      

   -- Soma de numeros complexos
   ------------------------------------------------
	FUNCTION ComplexSum (ValueA, ValueB: Complex_Type) RETURN Complex_Type;
	------------------------------------------------

    -- Multiplciação de numeros complexos
	------------------------------------------------
	FUNCTION ComplexMULT (ValueA, ValueB: Complex_Type) RETURN Complex_Type;
	------------------------------------------------
	

	
      end package Packages_Util;
       
      -- Package Body Section
      package body Packages_Util is
       
	
	------------------------------------------------
	--Calcula a soma de dois numeros complexos
	FUNCTION ComplexSum (ValueA, ValueB: Complex_Type) RETURN Complex_Type IS
		
		VARIABLE Result : Complex_Type;
        VARIABLE Natural_result : signed(31 downto 0);
        VARIABLE Complex_result : signed(31 downto 0);
    
	BEGIN
	
		Natural_result := ValueA.r + ValueB.r;
		Complex_result := ValueA.i + ValueB.i;
		
        Result.r := Natural_result(31 downto 0);
        Result.i := Complex_result(31 downto 0);
		RETURN Result;
		
	END ComplexSum;
	

	------------------------------------------------
	-- Calcula o produto entre dois numeros complexos
    FUNCTION ComplexMult(ValueA, ValueB: Complex_Type) RETURN Complex_Type IS
        
        VARIABLE Result: Complex_Type;
        VARIABLE Natural_result_first : signed(63 downto 0);
        VARIABLE Complex_result_first : signed(63 downto 0);
        VARIABLE Natural_result_second : signed(63 downto 0);
        VARIABLE Complex_result_second : signed(63 downto 0);   
        VARIABLE Natural_result : signed(63 downto 0);
        VARIABLE Complex_result : signed(63 downto 0); 
    BEGIN
    
        Natural_result_first := signed(ValueA.r) *signed(ValueB.r)/4096 ;
        Complex_result_first := signed(ValueA.r) *signed(ValueB.i)/4096 ;
   
        Natural_result_second :=  signed(ValueA.i)*signed(ValueB.i)/4096;
        Complex_result_second :=  signed(ValueA.i)*signed(ValueB.r)/4096;
     
     
        Natural_result := Natural_result_first- Natural_result_second;
        Complex_result := Complex_result_first+ Complex_result_second;
     
        Result.r := Natural_result(31 downto 0);
        Result.i := Complex_result(31 downto 0);
    
        RETURN Result;
    END ComplexMult;
	
 
    
END package body Packages_Util;