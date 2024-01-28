 ;_______________________________________________________________________________________;
;		Fakulteti i Inxhinierise Elektrike dhe Kompjuterike			;
;			Mikroprocesoret dhe Mikrokontrolleret     		        ;
;	    Regjistrimi i karakteristikave te elementeve ne temperatura te ndryshme    	;
;_______________________________________________________________________________________;

Display_Status	EQU	19h

bustherm equ p3.3

crcpolynom1wire equ 5dh
crc equ 5eh

Button_Next	BIT	P3.1
Button_OK	BIT	P3.2
Button_ESC	BIT	P3.3

Button_0_7	EQU	P2
Button_0	BIT	P2.0
Button_1	BIT	P2.1
Button_2	BIT	P2.2
Button_3	BIT	P2.3
Button_4	BIT	P2.4
Button_5	BIT	P2.5
Button_6	BIT	P2.6
Button_7	BIT	P2.7
Button_8	BIT	P3.7
Button_9	BIT	P3.6

clk 		equ	p1.7		
sdo 		equ	p1.6
sdi 		equ	p1.5
cs 		equ	p3.5
csDA 		equ 	p3.4

DIS_0		EQU	37H
DIS_1		EQU	38H
DIS_2		EQU	39H
DIS_3		EQU	3AH

DATA_BANK	EQU	3BH

Mijeshet	EQU	3CH
Qindeshet	EQU	3DH
Dhjeteshet	EQU	3EH
Njeshet		EQU	3FH
Vlera7SegH	EQU	40h
Vlera7Seg	EQU	41h

DAh 		equ	42h		
DAl 		equ	43h
ADh 		equ	44h
ADl 		equ	45h
temp 		equ	46h

C0		EQU	47H
C1		EQU	48H
C2		EQU	49H
C3		EQU	4aH

P1_GJT		EQU     55H
P1_GJP		EQU	56H

Kanali		EQU	57H

GjendjaDergimit		EQU	59H
GjendjaModbus		EQU	5AH  
CounterModbus		EQU	5BH

AD0H		EQU	71h
AD0L		EQU	72h

;MODBUS Slave for 8051
StartAddress    	EQU  	70H
ShareMemory		EQU	20H
CRC_ACCUM_LOW		EQU  	0DH 		;bank 1 regjistri R5
CRC_ACCUM_HI		EQU  	0EH 		;bank 1 regjistri R6
CRC_MASK_LSB  		EQU  	001H 		;CRC-16 polinomi
CRC_MASK_MSB  		EQU  	0A0H
COIL_ADDR  		EQU  	20H
INP_ADDR  		EQU  	20H
HREG_ADDR  		EQU  	20H
IREG_ADDR  		EQU  	20H
HREGNUM  		EQU  	20H		;NumriTotal i Regjistrave
IREGNUM  		EQU  	20H
INPNUM   		EQU  	128
COILNUM  		EQU  	128 		;numri total i coilave

t15   			EQU	63817 		;65536-1719 , 11bit/9600*1.5=63817
t35   			EQU  	61526 		;65536-4010 =61526
t20   			EQU  	15366

	ORG 00H
  		LJMP MAIN
	ORG 0BH
  		LJMP TMR0VEC
	ORG 1BH
	 	LJMP Interrupt_7seg
  	ORG 23H
    		LJMP SERVEC
 	ORG 30H
;-----------------------------------------------------------------------------------------------
MAIN:
 	MOV P1_GJT , #00			;gjendjet fillestare per shfaqjen e displayve
	MOV P1_GJP , #00

	MOV SP , #0B0H				;Inicializimi i StackPointerit
  	MOV GjendjaModbus , #01H
 	MOV CounterModbus , #00H
 	MOV SCON,#50H 				;TI=1, RI=0, REN=1
 	MOV T2CON,#34H
 	MOV RCAP2H,#0FFH
 	MOV RCAP2L , #0E6H
 	SETB TR2
 	MOV TMOD,#21H 				;TIMER1: MOD2-AUTORELOAD, TIMER0: MOD1-16BIT
 	MOV TH0,#HIGH t35
 	MOV TL0,#LOW t35
 	MOV TH1 , #58H
 	SETB TR0
 	SETB TR1
 	MOV IE,#82H 				;lejohet interapti i Timer0 dhe i Portit Serik
 	SETB ET0
 	SETB ES
 	SETB ET1

	MOV ADH,#00h
	MOV ADL,#00h
	MOV AD0H,#00h
	MOV AD0L,#00h
	MOV DAH, #00h
	MOV DAL, #00h
	MOV temp, #00h


	MOV Display_Status,#00H

	MOV mijeshet,#00H
	MOV Qindeshet,#00H
	MOV Dhjeteshet,#00H
	MOV Njeshet,#00H

	MOV DATA_BANK,#00H

	LCALL	Delay_Max
;---------------------------------------------------------------------------------------------------------------
Program:
	NOP
LJMP Program

;----------------------------
;vendos tensionin ne DAC dhe lexon tensionin ne ADC dhe temperaturen
;----------------------------
Mat_vlerat:
lcall Write_DA
lcall read_AD
lcall read_AD0
lcall lexo_temp
ret

;----------------------------
;vendos ne DA 10 vlera te njepasnjeshme te tensionit prej 0-4V, dhe mat vlerat perkatese te tensionit ne AD
;lexon temperaturen e ambientit ne te cilin gjenden elementet
;thirret gjate komunikimit me PC permes portit Serik
;----------------------------
Dergo_PC:
mov r7,#10
mov dah,#0
mov dal,#0
mov r0, #20h

prapa:
lcall Inc_400
LCALL Write_da	

lcall read_ad
mov @r0, adh
inc r0
mov @r0, adl


inc r0
djnz r7, prapa
lcall Lexo_Temp		
mov 34h, 67h
inc r0
mov 35h, 68h

RET
;----------------------------
Dergo_PC0:

mov r7,#10
mov dah,#0
mov dal,#0
mov r0, #20h

prapa0:
lcall Inc_400
LCALL Write_da	

lcall read_ad0
mov @r0, ad0h
inc r0
mov @r0, ad0l


inc r0
djnz r7, prapa0
lcall Lexo_Temp		
mov 34h, 67h
inc r0
mov 35h, 68h

RET
;----------------------------
;vendosja e vleres ne DA, varesisht prej vlerave ne DAh dhe DAl
;----------------------------
Write_DA:		

clr csDA
clr clk
mov a,DAh
;clr sdi ;bit0 - A/B
rlc a
mov sdi,c
setb clk
clr clk
;setb sdi ;bit1 - BUF
rlc a
mov sdi,c
setb clk
clr clk
;setb sdi ;bit2 - GAIN
rlc a
mov sdi,c
setb clk
clr clk
;setb sdi ;bit3 - SHDN
rlc a
mov sdi,c
setb clk
clr clk

;clr sdi ;bit4 - D11
rlc a
mov sdi,c
setb clk
clr clk
;setb sdi ;bit5 - D10
rlc a
mov sdi,c
setb clk
clr clk
;clr sdi ;bit6 - D9
rlc a
mov sdi,c
setb clk
clr clk
;clr sdi ;bit7 - D8
rlc a
mov sdi,c
setb clk
clr clk
mov a,DAl
;clr sdi ;bit8 - D7
rlc a
mov sdi,c
setb clk
clr clk
;clr sdi ;bit9 - D6
rlc a
mov sdi,c
setb clk
clr clk
;clr sdi ;bit10 - D5
rlc a
mov sdi,c
setb clk
clr clk
;clr sdi ;bit11 - D4
rlc a
mov sdi,c
setb clk
clr clk
;clr sdi ;bit12 - D3
rlc a
mov sdi,c
setb clk
clr clk

;clr sdi ;bit13 - D2
rlc a
mov sdi,c
setb clk
clr clk
;clr sdi ;bit14 - D1
rlc a
mov sdi,c
setb clk
clr clk
;clr sdi ;bit15 - D0
rlc a
mov sdi,c
setb clk
;clr clk
setb csDA
RET

;-------------------------------------
;Leximi i tensionit ne AD, dhe vendosja e rezultatit ne ADh dhe ADl
;-------------------------------------
Read_AD:

clr cs
clr clk
setb sdi ;bit0 - Start
setb clk
clr clk
setb sdi ;bit1 - SGL
setb clk
clr clk
clr sdi ;bit2 - D2
setb clk
clr clk
clr sdi ;bit3 - D1		0
setb clk
clr clk
setb sdi ;bit4 - D0		1			
setb clk			
clr clk

setb clk
clr clk
mov c,sdo ;bit5 - Null Bit

mov A,#0
;mov a.4,c
setb clk
clr clk
mov c,sdo ;bit6 - B11

mov a.3,c
setb clk
clr clk
mov c,sdo ;bit7 - B10

mov a.2,c
setb clk
clr clk
mov c,sdo ;bit8 - B9

mov a.1,c
setb clk
clr clk
mov c,sdo ;bit9 - B8

mov a.0,c
mov ADh,a

setb clk
clr clk
mov c,sdo ;bit10 - B7

mov a.7,c
setb clk
clr clk
mov c,sdo ;bit11 - B6

mov a.6,c
setb clk
clr clk
mov c,sdo ;bit12 - B5

mov a.5,c
setb clk
clr clk
mov c,sdo ;bit13 - B4


mov a.4,c
setb clk
clr clk
mov c,sdo ;bit14 - B3

mov a.3,c
setb clk
clr clk
mov c,sdo ;bit15 - B2

mov a.2,c
setb clk
clr clk
mov c,sdo ;bit16 - B1

mov a.1,c
setb clk
clr clk
mov c,sdo ;bit17 - B0

mov a.0,c
setb clk
;clr clk
setb cs
mov ADl,a

nop
nop
                                                                                                                                        RET

Read_AD0:

clr cs
clr clk
setb sdi ;bit0 - Start
setb clk
clr clk
setb sdi ;bit1 - SGL
setb clk
clr clk
clr sdi ;bit2 - D2
setb clk
clr clk
clr sdi ;bit3 - D1		0
setb clk
clr clk
clr sdi ;bit4 - D0		0			
setb clk			
clr clk

setb clk
clr clk
mov c,sdo ;bit5 - Null Bit

mov A,#0
;mov a.4,c
setb clk
clr clk
mov c,sdo ;bit6 - B11

mov a.3,c
setb clk
clr clk
mov c,sdo ;bit7 - B10

mov a.2,c
setb clk
clr clk
mov c,sdo ;bit8 - B9

mov a.1,c
setb clk
clr clk
mov c,sdo ;bit9 - B8

mov a.0,c
mov AD0h,a

setb clk
clr clk
mov c,sdo ;bit10 - B7

mov a.7,c
setb clk
clr clk
mov c,sdo ;bit11 - B6

mov a.6,c
setb clk
clr clk
mov c,sdo ;bit12 - B5

mov a.5,c
setb clk
clr clk
mov c,sdo ;bit13 - B4


mov a.4,c
setb clk
clr clk
mov c,sdo ;bit14 - B3

mov a.3,c
setb clk
clr clk
mov c,sdo ;bit15 - B2

mov a.2,c
setb clk
clr clk
mov c,sdo ;bit16 - B1

mov a.1,c
setb clk
clr clk
mov c,sdo ;bit17 - B0

mov a.0,c
setb clk
;clr clk
setb cs
mov AD0l,a

nop
nop
                                                                                                                                        RET

;------------------------------------
;leximi i temperatures prej sensorit DS18B20
;------------------------------------
Lexo_Temp:
mov crcpolynom1wire,#00110001b	; vlera e polinomit per llogaritjen e CRC8 1wire
nop
lcall threset
mov a,#33h
lcall thwrite
lcall readromfromslave		;lexohet ROM i slave-it ds18b20 dhe ruhet ne adresat perkatese
crcromwrong:
mov crc,#00h
mov a,5fh
lcall crc_1wire_generator
mov a,60h
lcall crc_1wire_generator
mov a,61h
lcall crc_1wire_generator
mov a,62h
lcall crc_1wire_generator
mov a,63h
lcall crc_1wire_generator
mov a,64h
lcall crc_1wire_generator
mov a,65h
lcall crc_1wire_generator

mov a,crc
cjne a,66h,crcromwrong		;kontrollohet se a jane crc e rom-it te njejta me te masterit dhe slave-it
nop
lcall threset
lcall matchrom			;dergohet ROMi i slavit qe te punohet vetem me slavin perkates
mov a,#4eh			;komanda write scratchpad
lcall thwrite
mov a,#0ffh			;th=255
lcall thwrite
mov a,#00h			;tl=0
lcall thwrite
mov a,#00011111b		;config register resolucioni 9bitsh
lcall thwrite
nop

lcall threset
lcall matchrom
mov a,#44h			;convert T komanda
lcall thwrite
lcall converteddelay		;vonese 99ms
nop
lcall threset
lcall matchrom
lcall scratchpadread

crcscratchpadwrong:
mov crc,#00h
mov a,67h
lcall crc_1wire_generator
mov a,68h
lcall crc_1wire_generator
mov a,69h
lcall crc_1wire_generator
mov a,6ah
lcall crc_1wire_generator
mov a,6bh
lcall crc_1wire_generator
mov a,6ch
lcall crc_1wire_generator
mov a,6dh
lcall crc_1wire_generator
mov a,6eh
lcall crc_1wire_generator
mov a,crc
cjne a,6fh,crcscratchpadwrong

RET

;-----------------------------------
;rrit vleren e DA per 400
;-----------------------------------
Inc_400:	
push acc
clr c
mov a,dal
add a,#90h	
mov dal, a
mov a, dah
addc a,#1	
orl a,#01110000b
mov dah, a

pop acc
RET
;---------------------------------------------------------------------------------------------------------------
Delay_max:
	MOV	R2, #07Ch
	MOV	R1, #07h
	MOV	R0, #019h
	NOP
	DJNZ	R0, $
	DJNZ	R1, $-5
	DJNZ	R2, $-9
	MOV	R0, #0E3h
	DJNZ	R0, $
	NOP
RET

;---------------------------------------------------------------------------------------------------------------
Delay_10ms:
	MOV	R2, #00Ch
	MOV	R1, #0EEh
	MOV	R0, #01Ah
	NOP
	DJNZ	R0, $
	DJNZ	R1, $-5
	DJNZ	R2, $-9
	MOV	R0, #00Dh
	DJNZ	R0, $
RET
;-----------------------------------------------  7Seg Interrupt  ----------------------------------------------
Interrupt_7seg:
	PUSH ACC

	MOV A,Display_Status
	RL A
	RL A
	MOV DPTR , #TAB_Status
	JMP @A+DPTR

TAB_Status:
	LJMP Display_Menu			;00
	NOP
	LJMP Display_Read_DA			;01	; Display_Shfaq_DA
	NOP
	LJMP Display_Read_AD			;02	; Dsipaly_Shfaq_AD
	NOP
	LJMP Display_Lexo_Temp			;03	; Display_Temp
	NOP
	LJMP Display_Write_DA			;04	; Dispaly_Write_DA
	NOP
	LJMP Display_Read_Get_Number		;05
	NOP
	LJMP Display_Read_Show_DA		;06
	NOP
	LJMP Display_Read_Show_AD		;07
	NOP
	LJMP Display_Read_Show_Temp		;08
	NOP
	LJMP Display_Read_AD0			;09
	NOP
	LJMP Display_Read_AD1			;10
	NOP
	LJMP Display_Read_Show_AD0		;11
	NOP
	LJMP END_7Seg_Int
	
;------------------------------------------------------------------------------------------------------------------
Display_Menu:					;00
		MOV DIS_3,#00110111B	;M
		MOV DIS_2,#01111001B	;E
		MOV DIS_1,#01010100B	;n
		MOV DIS_0,#00011100B	;u
		LCALL SHOW_7SEG

			JB Button_OK,END_D00
				MOV Display_Status,#01
				LCALL Clean_LCD
				JNB Button_OK,$
END_D00:	LJMP END_7Seg_Int
;------------------------------------------------------------------------------------------------------------------

Display_Read_DA:				;01
		MOV DIS_3,#10000110B	;1.
		MOV DIS_2,#10111000B	;L.
		MOV DIS_1,#00111111B	;D
		MOV DIS_0,#01110111B	;A
		LCALL SHOW_7SEG

			JB Button_Next,OK_D01
				MOV Display_Status,#02
				LCALL Clean_LCD
				JNB Button_Next,$
OK_D01:			JB Button_OK,ESC_D01
				MOV Display_Status,#06 ;03		;Display vleren e DA
				LCALL Clean_LCD
				JNB Button_OK,$
ESC_D01:		JB Button_ESC,END_D01
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
END_D01:	LJMP END_7Seg_Int
;------------------------------------------------------------------------------------------------------------------

Display_Read_AD:				;02
		MOV DIS_3,#11011011B	;2.
		MOV DIS_2,#10111000B	;L.
		MOV DIS_1,#01110111B	;A
		MOV DIS_0,#00111111B	;D
		LCALL SHOW_7SEG

			JB Button_Next,OK_D02
				MOV Display_Status,#03
				LCALL Clean_LCD
				JNB Button_Next,$
OK_D02:			JB Button_OK,ESC_D02
				MOV Display_Status,#09 ;03		;Zgjedh AD
				LCALL Clean_LCD
				JNB Button_OK,$
ESC_D02:		JB Button_ESC,END_D02
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
END_D02:		LJMP END_7Seg_Int
;------------------------------------------------------------------------------------------------------------------

Display_Lexo_Temp:				;03
		MOV DIS_3,#11001111B	;3.
		MOV DIS_2,#10111000B	;L.
		MOV DIS_1,#00000000B	;
		MOV DIS_0,#01111000B	;t
		LCALL SHOW_7SEG

			JB Button_Next,OK_D03
				MOV Display_Status,#04
				LCALL Clean_LCD
				JNB Button_Next,$
OK_D03:			JB Button_OK,ESC_D03
				MOV Display_Status,#08 		;Dispaly vleren e T
				LCALL Clean_LCD
				JNB Button_OK,$
ESC_D03:		JB Button_ESC,END_D03
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
END_D03:		LJMP END_7Seg_Int
;------------------------------------------------------------------------------------------------------------------
Display_Write_DA:				;04
		MOV DIS_3,#11100110B	;4.
		MOV DIS_2,#11101101B	;S.
		MOV DIS_1,#00111111B	;D
		MOV DIS_0,#01110111B	;A
		LCALL SHOW_7SEG

			JB Button_Next,OK_D04
				MOV Display_Status,#01
				LCALL Clean_LCD
				JNB Button_Next,$
OK_D04:			JB Button_OK,ESC_D04
				MOV Display_Status,#05		;Merr vleren qe duhet te shkruhet ne DA
				LCALL Clean_LCD
				JNB Button_OK,$
ESC_D04:		JB Button_ESC,END_D04
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
END_D04:	LJMP END_7Seg_Int

;------------------------------------------------------------------------------------------------------------------
Display_Read_Get_Number:			;05
		CLR TR1

;------------------------------
;leximi i numrit te pare (Display 3), per vleren e DA
;------------------------------
Dis3_s:
		JNB BUTTON_0,CI_B0_D3
		JNB BUTTON_1,CI_B1_D3
		JNB BUTTON_2,CI_B2_D3
		JNB BUTTON_3,CI_B3_D3
		JNB BUTTON_4,CI_B4_D3
		MOV DIS_3,#00001000B	;_		;Leximi i numrit te pare te DA
		MOV DIS_2,#00000000B	;
		MOV DIS_1,#00000000B	;
		MOV DIS_0,#00000000B	;
		LCALL Show_7seg
		SJMP Dis3_s
CI_B0_D3:
		LCALL Clean_LCD
		JNB BUTTON_0,$
		MOV DIS_3,#00111111B	;0
		MOV C3,#0
		SJMP Next_DIS2_CI
CI_B1_D3:
		LCALL Clean_LCD
		JNB BUTTON_1,$
		MOV DIS_3,#00000110B	;1
		MOV C3,#1
		SJMP Next_DIS2_CI
CI_B2_D3:
		LCALL Clean_LCD
		JNB BUTTON_2,$
		MOV DIS_3,#01011011B	;2
		MOV C3,#2
		SJMP Next_DIS2_CI
CI_B3_D3:
		LCALL Clean_LCD
		JNB BUTTON_3,$
		MOV DIS_3,#01001111B	;3
		MOV C3,#3
		SJMP Next_DIS2_CI
CI_B4_D3:
		LCALL Clean_LCD
		JNB BUTTON_4,$
		MOV DIS_3,#01100110B	;4
		MOV C3,#4
		SJMP Next_DIS2_CI

;------------------------------
;leximi i numrit te dyte (Display 2), per vleren e DA
;------------------------------
Next_DIS2_CI:	MOV A, C3
		CJNE A, #4, C3_JO4	; nese numri i pare i DA eshte 4, numri i dyte duhet te jete 0
		MOV DIS_2,#00111111B	;0
		MOV C2,#0
		LJMP Next_DIS1_CI
C3_JO4:
		MOV P2,#0FFH
		JNB BUTTON_0,CI_B0_D2
		JNB BUTTON_1,CI_B1_D2
		JNB BUTTON_2,CI_B2_D2
		JNB BUTTON_3,CI_B3_D2
		JNB BUTTON_4,CI_B4_D2
		JNB BUTTON_5,CI_B5_D2
		JNB BUTTON_6,CI_B6_D2
		JNB BUTTON_7,CI_B7_D2
		JNB BUTTON_8,MakeLong_CI_B8_D2
		JNB BUTTON_9,MakeLong_CI_B9_D2
		MOV DIS_2,#00001000B	;_		;Lexo numrin e dyte
		MOV DIS_1,#00000000B	;
		MOV DIS_0,#00000000B	;
		LCALL Show_7seg
		SJMP Next_DIS2_CI
	MakeLong_CI_B8_D2:	LJMP CI_B8_D2
	MakeLong_CI_B9_D2:	LJMP CI_B9_D2
;----------
CI_B0_D2:
		LCALL Clean_LCD
		JNB BUTTON_0,$
		MOV DIS_2,#00111111B	;0
		MOV C2,#0
		SJMP Next_DIS1_CI
CI_B1_D2:
		LCALL Clean_LCD
		JNB BUTTON_1,$
		MOV DIS_2,#00000110B	;1
		MOV C2,#1
		SJMP Next_DIS1_CI
CI_B2_D2:
		LCALL Clean_LCD
		JNB BUTTON_2,$
		MOV DIS_2,#01011011B	;2
		MOV C2,#2
		SJMP Next_DIS1_CI
CI_B3_D2:
		LCALL Clean_LCD
		JNB BUTTON_3,$
		MOV DIS_2,#01001111B	;3
		MOV C2,#3
		SJMP Next_DIS1_CI
CI_B4_D2:
		LCALL Clean_LCD
		JNB BUTTON_4,$
		MOV DIS_2,#01100110B	;4
		MOV C2,#4
		SJMP Next_DIS1_CI
CI_B5_D2:
		LCALL Clean_LCD
		JNB BUTTON_5,$
		MOV DIS_2,#01101101B	;5
		MOV C2,#5
		SJMP Next_DIS1_CI
CI_B6_D2:
		LCALL Clean_LCD
		JNB BUTTON_6,$
		MOV DIS_2,#01111101B	;6
		MOV C2,#6
		SJMP Next_DIS1_CI
CI_B7_D2:
		LCALL Clean_LCD
		JNB BUTTON_7,$
		MOV DIS_2,#00000111B	;7
		MOV C2,#7
		SJMP Next_DIS1_CI
CI_B8_D2:
		LCALL Clean_LCD
		JNB BUTTON_8,$
		MOV DIS_2,#01111111B	;8
		MOV C2,#8
		SJMP Next_DIS1_CI
CI_B9_D2:
		LCALL Clean_LCD
		JNB BUTTON_9,$
		MOV DIS_2,#01101111B	;9
		MOV C2,#9
		SJMP Next_DIS1_CI

;------------------------------
;leximi i numrit te trete (Display 1), per vleren e DA
;------------------------------
Next_DIS1_CI:	MOV P2,#0FFH
		JNB BUTTON_0,CI_B0_D1
		JNB BUTTON_1,CI_B1_D1
		JNB BUTTON_2,CI_B2_D1
		JNB BUTTON_3,CI_B3_D1
		JNB BUTTON_4,CI_B4_D1
		JNB BUTTON_5,CI_B5_D1
		JNB BUTTON_6,CI_B6_D1
		JNB BUTTON_7,CI_B7_D1
		JNB BUTTON_8,MakeLong_CI_B8_D1
		JNB BUTTON_9,MakeLong_CI_B9_D1
		MOV DIS_1,#00001000B	;_
		MOV DIS_0,#00000000B	;
		LCALL Show_7seg
		SJMP Next_DIS1_CI
	MakeLong_CI_B8_D1:	LJMP CI_B8_D1
	MakeLong_CI_B9_D1:	LJMP CI_B9_D1
;----------
CI_B0_D1:
		LCALL Clean_LCD
		JNB BUTTON_0,$
		MOV DIS_1,#00111111B	;0
		MOV C1,#0
		SJMP Next_DIS0_CI
CI_B1_D1:
		LCALL Clean_LCD
		JNB BUTTON_1,$
		MOV DIS_1,#00000110B	;1
		MOV C1,#1
		SJMP Next_DIS0_CI
CI_B2_D1:
		LCALL Clean_LCD
		JNB BUTTON_2,$
		MOV DIS_1,#01011011B	;2
		MOV C1,#2
		SJMP Next_DIS0_CI
CI_B3_D1:
		LCALL Clean_LCD
		JNB BUTTON_3,$
		MOV DIS_1,#01001111B	;3
		MOV C1,#3
		SJMP Next_DIS0_CI
CI_B4_D1:
		LCALL Clean_LCD
		JNB BUTTON_4,$
		MOV DIS_1,#01100110B	;4
		MOV C1,#4
		SJMP Next_DIS0_CI
CI_B5_D1:
		LCALL Clean_LCD
		JNB BUTTON_5,$
		MOV DIS_1,#01101101B	;5
		MOV C1,#5
		SJMP Next_DIS0_CI
CI_B6_D1:
		LCALL Clean_LCD
		JNB BUTTON_6,$
		MOV DIS_1,#01111101B	;6
		MOV C1,#6
		SJMP Next_DIS0_CI
CI_B7_D1:
		LCALL Clean_LCD
		JNB BUTTON_7,$
		MOV DIS_1,#00000111B	;7
		MOV C1,#7
		SJMP Next_DIS0_CI
CI_B8_D1:
		LCALL Clean_LCD
		JNB BUTTON_8,$
		MOV DIS_1,#01111111B	;8
		MOV C1,#8
		SJMP Next_DIS0_CI
CI_B9_D1:
		LCALL Clean_LCD
		JNB BUTTON_9,$
		MOV DIS_1,#01101111B	;9
		MOV C1,#9
		SJMP Next_DIS0_CI

;------------------------------
;leximi i numrit te katert (Display 0), per vleren e DA
;------------------------------
Next_DIS0_CI:	


		MOV P2,#0FFH
		JNB BUTTON_0,CI_B0_D0
		JNB BUTTON_1,CI_B1_D0
		JNB BUTTON_2,CI_B2_D0
		JNB BUTTON_3,CI_B3_D0
		JNB BUTTON_4,CI_B4_D0
		JNB BUTTON_5,CI_B5_D0

		mov a, c3
		cjne a, #4, c1_jo9
		MOV A, C1
		CJNE A, #9, C1_JO9
		MOV DIS_0,#00001000B	;_
		LCALL Show_7seg
		SJMP NEXT_DIS0_CI

C1_JO9:		JNB BUTTON_6,CI_B6_D0
		JNB BUTTON_7,CI_B7_D0
		JNB BUTTON_8,CI_B8_D0
		JNB BUTTON_9,MakeLong_CI_B9_D0
		MOV DIS_0,#00001000B	;_
		LCALL Show_7seg
		SJMP Next_DIS0_CI

	MakeLong_CI_B9_D0:	LJMP CI_B9_D0
;----------
CI_B0_D0:
		LCALL Clean_LCD
		JNB BUTTON_0,$
		MOV DIS_0,#00111111B	;0
		MOV C0,#0
		SJMP CI_END
CI_B1_D0:
		LCALL Clean_LCD
		JNB BUTTON_1,$
		MOV DIS_0,#00000110B	;1
		MOV C0,#1
		SJMP CI_END
CI_B2_D0:
		LCALL Clean_LCD
		JNB BUTTON_2,$
		MOV DIS_0,#01011011B	;2
		MOV C0,#2
		SJMP CI_END
CI_B3_D0:
		LCALL Clean_LCD
		JNB BUTTON_3,$
		MOV DIS_0,#01001111B	;3
		MOV C0,#3
		SJMP CI_END
CI_B4_D0:
		LCALL Clean_LCD
		JNB BUTTON_4,$
		MOV DIS_0,#01100110B	;4
		MOV C0,#4
		SJMP CI_END
CI_B5_D0:
		LCALL Clean_LCD
		JNB BUTTON_5,$
		MOV DIS_0,#01101101B	;5
		MOV C0,#5
		SJMP CI_END
CI_B6_D0:
		LCALL Clean_LCD
		JNB BUTTON_6,$
		MOV DIS_0,#01111101B	;6
		MOV C0,#6
		SJMP CI_END
CI_B7_D0:
		LCALL Clean_LCD
		JNB BUTTON_7,$
		MOV DIS_0,#00000111B	;7
		MOV C0,#7
		SJMP CI_END
CI_B8_D0:
		LCALL Clean_LCD
		JNB BUTTON_8,$
		MOV DIS_0,#01111111B	;8
		MOV C0,#8
		SJMP CI_END
CI_B9_D0:
		LCALL Clean_LCD
		JNB BUTTON_9,$
		MOV DIS_0,#01101111B	;9
		MOV C0,#9
		SJMP CI_END
CI_END:

;---------------------------------------------
;vendosja e vleres ne DAh dhe DAl,  e llogaritur nga vlerat e lexuara me siper
;---------------------------------------------
			MOV A,C3		; shumezimi i numrit te pare me 1000
			MOV B,#100
			MUL AB
			mov r1, a	
			mov r2, b	
			
			mov b, #10
			mul ab		
			mov r1, a	
			mov a, r2	
			mov r2, b	
			mov b, #10
			mul ab
			add a, r2
			mov r2, a
			
						
			MOV A,C2		; shumezimi i numrit te dyte me 100
			MOV B,#100
			MUL AB
			add a, r1	
			mov r3, a
			mov a, b
			addc a, r2
			mov b, a
			mov a, r3
			mov r1, a
			mov r2, b
			

			MOV A,C1		; shumezimi i numrit te trete me 10
			MOV B,#10
			MUL AB
			add a, r1	
			mov r3, a
			mov a, b
			addc a, r2
			mov b, a
			mov a, r3
			mov r1, a
			mov r2, b
			
			MOV A,C0		; shumezimi i numrit te pare me 1
			MOV B,#1
			MUL AB
			add a, r1
			mov r3, a
			mov a, b
			addc a, r2
			mov b, a
			mov a, r3
			
			mov DAl, a
			mov a,b
			orl a, #01110000b
			mov DAh, a		
			
;==============================================================
;pas leximit te vleres se DA, thirret rutina "mat_vlerat", e cila vendos vleren ne DA, dhe lexon AD dhe temperaturen
;------------------------------------------
W_END2:
		LCALL SHOW_7SEG
			JB Button_Next,OK_D10
				SETB TR1
				MOV Display_Status,#00		;rikthimi ne MENU
				LCALL Clean_LCD
				JNB Button_Next,$
				lcall mat_vlerat		; vendosja e vleres ne DA, leximi i AD dhe temperatures
				LJMP Display_Menu
OK_D10:			JB Button_OK,ESC_D10
				SETB TR1
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_OK,$
				lcall mat_vlerat
				LJMP Display_Menu
ESC_D10:		JB Button_ESC,END_D10
				SETB TR1
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
				lcall mat_vlerat
				LJMP Display_MENU
END_D10:		LJMP W_END2

;========================================================================================================
;vendosja e vleres se shkruar ne DA, ne 7 segmentesh
;---------------------------------------
Display_Read_Show_DA:				;06
		MOV a, DAh
		anl a,#0fh
		MOV Vlera7SegH,a
		MOV Vlera7Seg,DAl
		LCALL Show_V4096	;konverton numrin heksadecimal ne decimal, dhe per shfaqje ne 7-segmentesh

		MOV DIS_3,MIJESHET	;
		MOV DIS_2,QINDESHET
		MOV DIS_1,DHJETESHET	;
		MOV DIS_0,NJESHET	;
		LCALL SHOW_7SEG
			JB Button_Next,OK_D06
				MOV Display_Status,#00		
				LCALL Clean_LCD
				JNB Button_Next,$
				LJMP Display_Menu
OK_D06:			JB Button_OK,ESC_D06
				MOV Display_Status,#00		
				LCALL Clean_LCD
				JNB Button_OK,$
				LJMP Display_Menu
ESC_D06:		JB Button_ESC,END_D06
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
				LJMP Display_Menu
END_D06:	LJMP END_7Seg_Int


;------------------------------------------------------------------------------------------------------------------
;vendosja e vleres se lexuar nga AD, ne 7 segmentesh
;---------------------------------------
Display_Read_Show_AD:				;07
		MOV a, adh
		anl a,#0fh
		MOV Vlera7SegH,a
		MOV Vlera7Seg,ADl
		LCALL Show_V4096

		
		MOV DIS_3,MIJESHET	;
		MOV DIS_2,QINDESHET
		MOV DIS_1,DHJETESHET	;
		MOV DIS_0,NJESHET	;
		LCALL SHOW_7SEG
			JB Button_Next,OK_D07
				MOV Display_Status,#00	
				LCALL Clean_LCD
				JNB Button_Next,$
				LJMP Display_Menu
OK_D07:			JB Button_OK,ESC_D07
				MOV Display_Status,#00	
				LCALL Clean_LCD
				JNB Button_OK,$
				LJMP Display_Menu
ESC_D07:		JB Button_ESC,END_D07
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
				LJMP Display_Menu
END_D07:	LJMP END_7Seg_Int

;------------------------------------------------------------------------------------------------------------------

Display_Read_Show_AD0:				;11

		MOV a, AD0h
		anl a,#0fh
		MOV Vlera7SegH,a
		MOV Vlera7Seg,AD0l
		LCALL Show_V4096

		
		MOV DIS_3,MIJESHET	;
		MOV DIS_2,QINDESHET
		MOV DIS_1,DHJETESHET	;
		MOV DIS_0,NJESHET	;
		LCALL SHOW_7SEG
			JB Button_Next,OK_D11
				MOV Display_Status,#00	
				LCALL Clean_LCD
				JNB Button_Next,$
				LJMP Display_Menu
OK_D11:			JB Button_OK,ESC_D11
				MOV Display_Status,#00	
				LCALL Clean_LCD
				JNB Button_OK,$
				LJMP Display_Menu
ESC_D11:		JB Button_ESC,END_D11
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
				LJMP Display_Menu
END_D11:	LJMP END_7Seg_Int

;------------------------------------------------------------------------------------------------------------------

;vendosja e vleres se lexuar nga sensori i temperatures, ne 7 segmentesh
;---------------------------------------
Display_Read_Show_Temp:				;08
		lcall temp_to_7segment
		LCALL SHOW_7SEG
			JB Button_Next,OK_D08
				MOV Display_Status,#00		
				LCALL Clean_LCD
				JNB Button_Next,$
				LJMP Display_Menu
OK_D08:			JB Button_OK,ESC_D08
				MOV Display_Status,#00		
				LCALL Clean_LCD
				JNB Button_OK,$
				LJMP Display_Menu
ESC_D08:		JB Button_ESC,END_D08
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
				LJMP Display_Menu
END_D08:	LJMP END_7Seg_Int
;------------------------------------------------------------------------------------------------------------------

Display_Read_AD0:				;09

		MOV DIS_3,#10111000B	;L.
		MOV DIS_2,#01110111B	;A
		MOV DIS_1,#00111111B	;D
		MOV DIS_0,#00111111B	;0
		LCALL SHOW_7SEG

			JB Button_Next,OK_D09
				MOV Display_Status,#10
				LCALL Clean_LCD
				JNB Button_Next,$
OK_D09:			JB Button_OK,ESC_D09
				MOV Display_Status,#11 ;03		;Lexo AD0
				LCALL Clean_LCD
				JNB Button_OK,$
ESC_D09:		JB Button_ESC,END_D09
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
END_D09:		LJMP END_7Seg_Int
;------------------------------------------------------------------------------------------------------------------

Display_Read_AD1:				;10
		
		MOV DIS_3,#10111000B	;L.
		MOV DIS_2,#01110111B	;A
		MOV DIS_1,#00111111B	;D
		MOV DIS_0,#00000110B	;1
		LCALL SHOW_7SEG

			JB Button_Next,OK_D010
				MOV Display_Status,#09
				LCALL Clean_LCD
				JNB Button_Next,$
OK_D010:			JB Button_OK,ESC_D010
				MOV Display_Status,#07 ;03		;Lexo AD1
				LCALL Clean_LCD
				JNB Button_OK,$
ESC_D010:		JB Button_ESC,END_D010
				MOV Display_Status,#00
				LCALL Clean_LCD
				JNB Button_ESC,$
END_D010:		LJMP END_7Seg_Int
;------------------------------------------------------------------------------------------------------------------
END_7Seg_Int:
	POP ACC
RETI
;------------------------------------------------------------------------------------------------------------------
Clean_LCD:
				PUSH DIS_3
				PUSH DIS_2
				PUSH DIS_1
				PUSH DIS_0

				MOV DIS_3,#00000000B	;
				MOV DIS_2,#00000000B	;
				MOV DIS_1,#00000000B	;
				MOV DIS_0,#00000000B	;
				LCALL SHOW_7SEG
				LCALL Delay_10ms

				POP DIS_0
				POP DIS_1
				POP DIS_2
				POP DIS_3
RET
;------------------------------------------------------------------------------------------------------------------
;Ndezja e segmenteve te caktuara ne 7-segmentesh varesisht nga vlerat e vendosura ne DIS_0, DIS_1,DIS_2, DIS_3
;----------------------------------------
SHOW_7SEG:
	MOV A , P1_GJT
	RL A
	RL A
	MOV DPTR , #TABPROC
	JMP @A+DPTR

TABPROC:
	LJMP P1_GJ00
	NOP
	LJMP P1_GJ01
	NOP
	LJMP P1_GJ02
	NOP
	LJMP P1_GJ03
	NOP

P1_GJ00:
	ANL P1 , #11110000B
	MOV P0 , DIS_0		;Dis 0
	SETB P1.0
	  MOV P1_GJT , #1
	LJMP P1_END

P1_GJ01:
	ANL P1 , #11110000B
	MOV P0 , DIS_1		;Dis 1
	SETB P1.1
	  MOV P1_GJT , #2
	LJMP P1_END

P1_GJ02:
	ANL P1 , #11110000B
	MOV P0 , DIS_2		;Dis 2
	SETB P1.2
	  MOV P1_GJT , #3
	LJMP P1_END

P1_GJ03:
	ANL P1 , #11110000B
	MOV P0 , DIS_3		;Dis 3
	SETB P1.3
	  MOV P1_GJT , #0
	LJMP P1_END

P1_END:
RET
;------------------------------------------------------------------------------------------------------------------
Show_V4096:
mov r1, vlera7segh
mov r0, vlera7seg

mov r3, #3h
mov r2, #0e8h

lcall div16
mov a, r2
lcall number_to_7seg
mov mijeshet, a

mov r3, #00h
mov r2, #100
lcall div16
mov a, r2
lcall number_to_7seg
mov qindeshet, a

mov r3, #00h
mov r2, #10
lcall div16
mov a, r2
lcall number_to_7seg
mov dhjeteshet, a

mov a, r0
lcall number_to_7seg
mov njeshet, a


RET
;------------------------------------------------------------------------------------------------------------------
;pjesetimi i numrave 16 bitesh
;----------------------------------------
div16:
  CLR C       
  MOV R4,#00h 
  MOV R5,#00h 
  MOV B,#00h  
div1:
  INC B      
  MOV A,R2   
  RLC A      
  MOV R2,A   
  MOV A,R3   
  RLC A      
  MOV R3,A   
  JNC div1   
div2:        
  MOV A,R3   
  RRC A      
  MOV R3,A   
  MOV A,R2   
  RRC A      
  MOV R2,A   
  CLR C      
  MOV 07h,R1 
  MOV 06h,R0 
  MOV A,R0   
  SUBB A,R2  
  MOV R0,A   
  MOV A,R1   
  SUBB A,R3  
  MOV R1,A   
  JNC div3   
  MOV R1,07h 
  MOV R0,06h
div3:
  CPL C      
  MOV A,R4 
  RLC A      
  MOV R4,A   
  MOV A,R5
  RLC A
  MOV R5,A		
  DJNZ B,div2 
  MOV R3,05h  
  MOV R2,04h  
  RET

;------------------------------------------------------------------------------------------------------------------
;caktimi i vlerave te display 7-segment, per numrat perkates decimal
;--------------------------------
Number_To_7seg:
	INC A
	MOVC A,@A+PC
	RET
n7segtab:
	db 00111111b	;0
	db 00000110b	;1
	db 01011011b	;2
	db 01001111b	;3
	db 01100110b	;4
	db 01101101b	;5
	db 01111101b	;6
	db 00000111b	;7
	db 01111111b	;8
	db 01101111b	;9
;------------------------------------------------------------------------------------------------------------------
;Rutinat per leximin e vleres se temperatures nga sensori DS18B20
;---------------------------------------------------------------------------------------
threset:
		      		;therm. bus reset signal and presence signal from slave   
mov a,#3
clr bustherm
mov b,#160
djnz b,$
setb bustherm
mov b,#4
clr c
waitl1:
jb bustherm,wh1
djnz b,waitl1
djnz acc,waitl1
sjmp short1
wh1:
mov b,#70
hl1:
orl c,/bustherm
djnz b,hl1
short1:


ret

thwrite:			;funksioni per shkruarje te slave ds18b20
push b
mov b,#8			;qe te paraqiten 8bita=2shifrahexa--> komandat e ds18b20
loop2:
rrc a				;biti lsb qe eshte ne A zhvendoset ne C, ndersa C ne msb e A
lcall bytetobit			;byte to bit perdoret sepse komunikimi eshte serik pra rend bitash
djnz b,loop2
pop b
ret

bytetobit:
clr bustherm
mov 7fh,#1			;vonese 1x1.5x2=3us
lcall vonesa
mov bustherm,c
mov 7fh,#20			;vonese 2x1.5x20=60us		
lcall vonesa
setb bustherm
mov 7fh,#20			;vonese 2x1.5x20=60us
lcall vonesa
ret

vonesa:				;vonese ne baze te vleres ne regjistrin 20h
djnz 7fh,vonesa
ret

readromfromslave:
	
lcall read_byte			;family code 28h					
mov 5fh,a				
lcall read_byte		
mov 60h,a			
lcall read_byte		
mov 61h,a			
lcall read_byte		
mov 62h,a			
lcall read_byte		
mov 63h,a			
lcall read_byte		
mov 64h,a			
lcall read_byte		
mov 65h,a
lcall read_byte		
mov 66h,a			;crc rom nga slave
			
ret


read_byte:			;funskioni per leximin e nje bajti


mov r3,#8			;unaze qe perseritet 8here pra per 8bita
mov a,#00h			;inicializim i accumulator-it			
u1:
lcall read_bit			;thirret funksioni per ruajtjen e bitave
rrc a				;biti qe gjendet ne C kalon ne vendin e MSB ne A
djnz r3,u1

ret


read_bit:			;funksioni per leximin e nje biti

push acc
clr bustherm			;busi vlere 0
nop
nop
nop
setb bustherm			;busi vlere 1
mov 7fh,#2			;vonesa 6us
lcall vonesa
mov c,bustherm			;vlera qe vie ne bus nga slave ruhet ne C
mov 7fh,#20			;vonese 60us
lcall vonesa 
pop acc
ret

matchrom:
mov a,#55h			;funksioni matchrom nenkupton nje komand te rom-it te ds18b20
				;komanda matchrom qe duhet te inicohet kurdo qe i adresomi ndonjerit prej slave
lcall thwrite				
mov a,5fh				
lcall thwrite		
mov a,60h			
lcall thwrite		
mov a,61h			
lcall thwrite		
mov a,62h			
lcall thwrite		
mov a,63h			
lcall thwrite		
mov a,64h			
lcall thwrite		
mov a,65h
lcall thwrite		
mov a,66h
lcall thwrite
			
ret

scratchpadread:			;funskioni per leximin e memories se brendshme te ds18b20
mov a,#0beh
lcall thwrite
nop
lcall read_byte				
mov 67h,a			;LSB i temperatures ruhet ne 27h regjisterin	
lcall read_byte		
mov 68h,a			;MSB i temperatures ruhet ne 28h regjistrin
lcall read_byte		
mov 69h,a			;Vlera e TH regjistrit ruhet ne 29h regjistrin
lcall read_byte		
mov 6ah,a			;Vlera e TL regjistrit ruhet ne 2ah regjistrin
lcall read_byte		
mov 6bh,a			;Vlera e configuration register ruhet ne 2bh regjistrin
lcall read_byte		
mov 6ch,a			
lcall read_byte		
mov 6dh,a
lcall read_byte		
mov 6eh,a
lcall read_byte		
mov 6fh,a			;byte 8 , byte i fundit i scratchpad-it, vlera e CRC e cila ruhet ne 2fh regjistrin
ret


converteddelay:			;vonese kur nevojitet komanda convert T per resoulucion 9 bitsh koha maksimale e komandes eshte 94ms
				;vonesa reale eshte 99ms

mov r0,#01
l3:
mov r1,#37
l2:
mov r2,#100
l1:
djnz r2,l1			;vonese 100x1.5x2=300us
djnz r1,l2			;vonese 037x1.5x2=111us
djnz r0,l3			;vonese 001x1.5x2=003us

ret




;========================================================================================================
crc_generator_modbus:


crcregister equ a		;A perdoret si regjistri per ruajtejen e crc pergjate tere procedures
mov crcregister,0ffh	;??	;A inicializohet si FFh
xrl a,67h			;x or logic crcregister me bytin e pare te scratchpadit
mov b,#8			;sherben per te treguar 8 shiftime
a1:
clr c				;gjate  shiftimit msb-ja e crcregjistrin duhet te jet 0
rrc a				;lsb e A pas shiftim kalon ne carry bit
djnz b,a1
jnc a1				;kontrollohet vlera e bitit te shiftuar te A, pra lsb-s paraprake
xrl a,crcpolynom1wire		;nese lsb ka qene 1 ateher kerkohen x or logic i crcs me polinomin e crc8 te 1wire
;
xrl a,68h
mov b,#8
a2:
clr c
rrc a
djnz b,a2
jnc a2
xrl a,crcpolynom1wire
;
xrl a,69h
mov b,#8
a3:
clr c
rrc a
djnz b,a3
jnc a3
xrl a,crcpolynom1wire

xrl a,6ah
mov b,#8
a4:
clr c
rrc a
djnz b,a4
jnc a4
xrl a,crcpolynom1wire
;

xrl a,6bh
mov b,#8
a5:
clr c
rrc a
djnz b,a5
jnc a5
xrl a,crcpolynom1wire
;
xrl a,6ch
mov b,#8
a6:
clr c
rrc a
djnz b,a6
jnc a6
xrl a,crcpolynom1wire
;
xrl a,6dh
mov b,#8
a7:
clr c
rrc a
djnz b,a7
jnc a7
xrl a,crcpolynom1wire
;
xrl a,6eh
mov b,#8
a8:
clr c
rrc a
djnz b,a8
jnc a8
xrl a,crcpolynom1wire
mov 7eh,a
ret

;======================================================================
;funksioni per gjenerimin e crc nga masteri per polinomin x8+x5+x4+1
;======================================================================

crc_1wire_generator: 
push acc         
push b  	 
push acc
mov b,#8       
crcloop:       
xrl a,crc
rrc a  	
mov a,crc      
jnc zero        
xrl a,#18h        
zero:
rrc a
mov crc,a
pop acc	
rr a  	
push acc       
djnz b,crcloop
pop acc 	
pop b
pop acc
ret

;=======================================================================================
; Vendosja e temperatures se lexuar nga sensori ne 7-segmentesh
;======================================================================================
temp_to_7segment:
clr c
mov a,68h
rlc a
jc negative

rlc a
rlc a
rlc a
rlc a
rlc a
mov b.6,c
rlc a
mov b.5,c
rlc a
mov b.4,c

mov a,67h
rlc a
mov b.3,c
rlc a
mov b.2,c
rlc a
mov b.1,c
rlc a
mov b.0,c

sjmp rutinadis0

rutinadis0:
rlc a
jnc zeroo
mov r0,#01101101b
ljmp rutinadis3pozitive
zeroo:
mov r0,#00111111b
ljmp rutinadis3pozitive

negative:
mov r3,#01000000b
rlc a
rlc a
rlc a
rlc a
rlc a
mov b.6,c
rlc a
mov b.5,c
rlc a
mov b.4,c

mov a,67h
rlc a
mov b.3,c
rlc a
mov b.2,c
rlc a
mov b.1,c
rlc a
mov b.0,c

rlc a
jnc zeroo0
mov r0,#01101101b
zeroo0:
mov r0,#00111111b

mov a,b
mov b,#01000000b
subb a,b
mov b,#10
div ab

rutinadis2negative:

lcall number_to_7seg
mov r2, a

zzz2:		;rutina dis1negative

mov a,b


lcall number_to_7seg
mov r1, a
ljmp zzz1



rutinadis3pozitive:


mov a,b
mov b,#100
div ab
kss:
cjne a,#00h,af0
mov r3,#00h
sjmp rutinadis2
af0:
cjne a,#01h,kss
mov r3,#00000110b

rutinadis2:
mov a,b
mov b,#10
div ab

lcall number_to_7seg
mov r2, a

zzz:			;rutinadis1
mov a,b

lcall number_to_7seg
mov r1, a
zzz1:

mov dis_0,r0
mov a, #10000000b
orl a, r1
mov dis_1,a
mov dis_2,r2
mov dis_3,r3

RET

;------------------------------------------------------------------------------------------------------------------
; Impelementimi i MODBUS protokollit
;======================================================================
;			INTERAPTI I TAJMERIT
;======================================================================
TMR0VEC:
	PUSH PSW
	PUSH 03H
	CLR RS0
	
	
	CLR TR0 		;ndalet tajmeri
	MOV R3 , GjendjaModbus
	CJNE R3,#01H,TOJO1
 	MOV GjendjaModbus , #02H
 	
 	POP 03H
 	POP PSW
 		RETI
TOJO1:
TOJO2:  
 	CJNE R3,#03H,TOJO3
 	MOV GjendjaModbus , #04H
 	CLR TR0 			;ndalet tajmeri0
 	CLR TF0 			;fshihet flagu
 	MOV TH0,#HIGH t20
 	MOV TL0,#LOW t20
 	SETB TR0

 	POP 03H
 	POP PSW
		RETI

TOJO3:  
	CJNE R3,#04H,TOJO4
	
	PUSH ACC
	PUSH B
	PUSH 00H
	PUSH 01H
	PUSH 02H
	PUSH 04H
	PUSH 05H
	PUSH 06H
	PUSH 07H
	
	CALL ANALIZA
	
	POP 07H
	POP 06H
	POP 05H
	POP 04H
	POP 02H
	POP 01H
	POP 00H
	POP B
	POP ACC
TOJO4: 
 	POP 03H
 	POP PSW
  		RETI

;======================================================================
;				RX INTERAPTI
;======================================================================
SERVEC: 
	PUSH PSW
	PUSH ACC
	PUSH B
	PUSH 00H
	PUSH 03H
	CLR RS0
		
	MOV R3 ,  GjendjaModbus
 	JB RI,RX
 	MOV A,CounterModbus			;rutina per dergim
 	CJNE A,GjendjaDergimit,NEXTCH 		;r2=02h
 	MOV GjendjaModbus , #02H
	MOV CounterModbus , #00H
	
 	POP 03H
  	POP 00H
  	POP B
 	POP ACC
	POP PSW
 	CLR TI
  		RETI

NEXTCH:
	MOV R0,#StartAddress
	MOV A , R0
	ADD A , GjendjaDergimit	
 	MOV R0 , A
 	MOV A , @R0
	
 	MOV SBUF,A
 	INC GjendjaDergimit
 	
 	POP 03H
 	POP 00H
 	POP B
 	POP ACC
	POP PSW
 	CLR TI
 		RETI

RX:  
 	CLR RI
 	CJNE R3,#01H,RXJO1
 	CLR TR0
 	MOV TH0,#HIGH t35
 	MOV TL0,#LOW t35
 	SETB TR0
 	
 	POP 03H
 	POP 00H
 	POP B
 	POP ACC
 	POP PSW
 		RETI
  
RXJO1: 
	CJNE R3,#02H,RXJO2
 	MOV CounterModbus , #00H 			;fshihet bufferi duke u pozicionuar ne 0 pas kalimit nga 2-3

RXGJ2:  
	CLR TR0
 	CLR TF0 			;flagu i tajmerit0
 	MOV TH0,#HIGH t15
 	MOV TL0,#LOW t15
 	SETB TR0
 	MOV GjendjaModbus , #03H
 	MOV A,SBUF 			;shkrimi ne buffer
 	
 	mov B , A
 	mov A ,#StartAddress
 	ADD A , CounterModbus
 	mov R0 , A
 	MOV A , B
 	mov @R0, A
 
 	INC CounterModbus
	
	POP 03H
 	POP 00H
 	POP B
 	POP ACC
 	POP PSW
 		RETI
  
RXJO2:  
 	CJNE R3,#03H,RXJO3
 	SJMP RXGJ2
RXJO3:  
 	CJNE R3,#04H,RXJO4
 	CLR TR0
 	CLR TF0
 	MOV TH0,#HIGH t35
 	MOV TL0,#LOW t35
 	SETB TR0
 	MOV GjendjaModbus , #01H

RXJO4:
  	POP 03H
 	POP 00H
 	POP B
	POP ACC
	POP PSW
 		RETI
;======================================================================
;				ANALIZA
;======================================================================
ANALIZA:
 	MOV A,CounterModbus
 	MOV R7,A 			;R7=R4 por ndyshon ne 0 gjate kalkulimit teCRC
 	DEC R7 				;zvoglojme per 2(e hjekim crc ngamesazhi)
 	DEC R7
 	CALL CALC_CRC
;krahsimi i crc

 	MOV A,#StartAddress
 	ADD A , R7
 	MOV R0 , A
 	MOV A , @R0
 
 
	CJNE A,CRC_ACCUM_LOW,ANALEND 	;shkon ne analend nese crcgabim
 
 	INC R0
 	MOV A , @R0
 	

 	CJNE A,CRC_ACCUM_HI,ANALEND 	;shkon ne analend nese crc gabim
;fundi i krahasimit (CRC-OK)
;krahasimi i adreses

	MOV R0 , #StartAddress
	MOV A ,@R0

 	CJNE A,#01H,ANALEND
 	JMP FUNKS
ANALEND:
 	JMP ANALEND2
 	
;fundi i krahasimit(AdresaOK)
;======================================================================
;			FUNKSIONI
;======================================================================	
FUNKS:  
	MOV R0 , #StartAddress+1
	MOV A , @R0

	CJNE A,#3,JOFO3
FO3:
 	INC R0
 	MOV A , @R0
 
 	JZ FO3_01
 	JMP STADDERR
 
FO3_01: 
 	INC R0
 	MOV A , @R0
 
 	MOV R6,A
 	CLR C
 	SUBB A,#HREGNUM 		;numri total i regjistrave
 	JC FO3_02
 	JMP STADDERR

FO3_02: 
 	INC R0
 	MOV A , @R0
 
 	JZ FO3_03
 	JMP VALERR

FO3_03: 
	INC R0
	MOV A , @R0
  
 	MOV R5,A
 	DEC A
 	ADD A,R6
 	CLR C
 	SUBB A,#HREGNUM 		;numri total i regjistrave
 	JC FO3_04
 	JMP VALERR
 
FO3_04: 
 	MOV CounterModbus,#02H
 	CALL READREG
 	JMP SENDMSG
	
JOFO3:  
 	CJNE A,#4,JOFO4
FO4:
 	JMP FO3

JOFO4:
 	CJNE A,#6,JOFO6

FO6:
	INC R0
 	MOV A , @R0
  
 	CJNE A,#0,FO6_E1
 
 	INC R0
 	MOV A , @R0

 	MOV R6,A
 	CLR C
 	SUBB A,#HREGNUM 		;numri total i regjistrave=hregnum
 	JNC FO6_E1
 
 	INC R0
 
 
 	CALL PRESREG
 	JMP SENDMSG1

FO6_E1:
 	JMP STADDERR

JOFO6:
 	CJNE A,#16,JOFO16

FO16:
	INC R0
 	MOV A , @R0
 
 	JZ FO16_01
 	JMP STADDERR

FO16_01:
 	INC R0
 	MOV A , @R0
 
 	MOV R6,A
 	CLR C
 	SUBB A,#HREGNUM 		;numri total i regjistrave
 	JC FO16_02
 	JMP STADDERR

FO16_02:
 	INC R0
 	MOV A , @R0
 
 	JZ FO16_03
 	JMP VALERR

FO16_03:
 	INC R0
 	MOV A , @R0
 
 	MOV R5,A
 	DEC A
 	ADD A,R6
 	CLR C
 	SUBB A,#HREGNUM 		;numri total i regjistrave
 	JC FO16_04
 	JMP VALERR

FO16_04:
 	MOV CounterModbus ,#06H
 	CALL PRESMREG
 	JMP SENDMSG

JOFO16:
;=================================Funksioni i ardhur gabim==============================;FUNCERR:
 	MOV A,#StartAddress
 	ADD A , #01
 	MOV R0 , A
 	MOV A , @R0

 	SETB ACC.7

 	MOV @R0 , A
 	INC R0
  
 	MOV A,#01H
 
 	MOV @R0 , A
 
 	MOV R4,#03H
 	JMP SENDMSG

ANALEND2:
 	RET 				;kthehet ne ANALIZA nga TOJO3

;============================Vlera e ardhur gabim======================================; 
VALERR:
 	MOV A,#StartAddress
 	ADD A , #01
 	MOV R0 , A
 	MOV A , @R0
 
 	SETB ACC.7
 
 	MOV @R0 , A
 	INC R0
 
 	MOV A,#03H
 
 	MOV @R0 , A
 
 	MOV R4,#03H
 	JMP SENDMSG 

;============================Adresa e ardhur gabim==================================;
STADDERR:
 	MOV A,#StartAddress
 	ADD A , #01
 	MOV R0 , A
 	MOV A , @R0
 
 	SETB ACC.7
  
 	MOV @R0 , A
 	INC R0
 	
 	MOV A,#02H
 	
 	MOV @R0 , A
 
 	MOV R4,#03H
 	JMP SENDMSG

SENDMSG:
 	MOV B , A
 	mov A ,#StartAddress
 	ADD A , CounterModbus
 	mov R0 , A
 	MOV A , B
 
 	MOV 07H,CounterModbus
 	CALL CALC_CRC
 	MOV A,0DH
 
 	MOV @R0 , A
 	INC R0
 
 	MOV A,0EH
 	MOV @R0 , A
 
 	INC CounterModbus
 	INC CounterModbus
;==========================Dergo mesazhin e njejt qe ka ardhur ==========================;
SENDMSG1: 				;reply i njejte tx=rx pa llogaritje
 	MOV GjendjaDergimit,#0 			;te CRC-se
 	SETB TI
 
  		RET

CALC_CRC:
;kalkulon crc ne karakteret e shkruar ne memorie extprej4000h dherezulratin
;e vendose ne 0D,0E praR5,R6banka1
;perdore R4=numri char te pranuar,R7=R4=DPL pastaj behet 0 dhe R6=8bitne bajt

 	MOV CRC_ACCUM_LOW,#0FFH 	;fshihet crc accum para fillimit
 	MOV CRC_ACCUM_HI,#0FFH
 
 	MOV R0 ,#StartAddress 
 	MOV B , R7
 	
 ;push 07H ;e ruajm R7=07H direkt
; XOR bajti i pare me bajtin e ulet te CRC
;
CC10:
 	MOV A , @R0
 
 	XRL A,CRC_ACCUM_LOW
 	MOV CRC_ACCUM_LOW,A
 	MOV R6,#8 			;R6 sherben si numrues i 8 bitave ne bajt
CC20:
 	MOV A , CRC_ACCUM_HI 		;merret bajti i larte
 	CLR C 				;mbushet me 0
 	RRC A 				;shiftohet djathtas
 	MOV CRC_ACCUM_HI , A
 	MOV A , CRC_ACCUM_LOW 		;dhe per bajtin e ulet
 	RRC A 				;shiftohet djathtas
 	MOV CRC_ACCUM_LOW,A
 	JNC CC30
 	MOV A , CRC_ACCUM_LOW
 	XRL A , #CRC_MASK_LSB
 	MOV CRC_ACCUM_LOW , A
 	MOV A , CRC_ACCUM_HI
 	XRL A , #CRC_MASK_MSB
 	MOV CRC_ACCUM_HI , A

CC30:
 	DJNZ R6 , CC20 			;perseritet tete here
 	INC R0
 
 	DJNZ R7 , CC10 			;nje bajt me pak per te llogarite
 	;pop 07H
 	MOV R7 , B

 		 RET

;preset i nje regjistri presreg
PRESREG:
	MOV A,R6
 	RL A
 	ADD A,#ShareMemory
 	INC A
 	MOV R1,A  ;******************** R0 eshte bere R1
 
 	MOV A , @R0
  
 	MOV @R1,A
 	DEC R1
 	INC R0
 	MOV A , @R0
 
 	MOV @R1,A
 	MOV A,20H
	CPL A 			;Bartja e regjistrit ne portin P1
	MOV P1,A 			;Percaktimi i logjikes 1/0,on/off=========================================

  		RET
;readreg
;leximi i regjistrave
;R6 start adress,R5 count
;=============================== Lexo regjister =========================================;
READREG:
	lcall dergo_pc		;rutina qe vendos vlerat e tensionit dhe temperatures per bartje ne PC

	
 	MOV A,R5
 	RL A

 	MOV B , A
 	mov A ,#StartAddress
 	ADD A , CounterModbus
 	mov R0 , A
 	MOV A , B
 	mov @R0, A
 	INC R0
 
 	INC CounterModbus
 	MOV A,R6
 	ADD A,#ShareMemory
 	MOV R1,A			;Ndryshim R0 eshte bere R1

RR_00:

 	MOV A,#00
 	MOV @R0 , A
 	INC R0
 
 	INC CounterModbus
 	MOV A,@R1

	MOV @R0 , A
 	INC R0
 
 	INC CounterModbus
 	INC 01H
 	DJNZ R5,RR_00

  		RET

;presreg=preset register
;shkruarja e regjistrave
;R6 start adress,R5 count
					
;=========================== Shkruaj ne disa regjistra ====================================;
PRESMREG:
	

 	MOV A ,#StartAddress
 	ADD A , CounterModbus
 	MOV R0 , A
 	INC R0			;R0 <- 7
 	MOV A,R6
 	ADD A,#ShareMemory
 	MOV R1,A

PMR_00:
	INC R0			;R0<-8
  	MOV A , @R0
 
 	MOV @R1,A ;****************	;R1 <- 20H
 	INC R0			;
 	INC 01H
 	DJNZ R5,PMR_00 ;****************
	
RET

END
