ECE382-Lab2
===========
Subroutines

#Objective

  Use subroutines to decrypt a message. The call and return commands will pass arguments to either of the subroutines. 

#PreliminaryDesign

  (https://github.com/EricaLewandowski/ECE382-Lab2/blob/master/Lab2Prelab.pdf)

#Algorithms/Code
```
          .text                           ; Assemble into program memory
            .retain                         ; Override ELF conditional linking
                                            ; and retain current section
            .retainrefs                     ; Additionally retain any sections
                                            ; that have references to current
                                            ; section
			.global RESET

            .data
			.bss		store, 0x100
            .text

ReqFxn:		.byte		0xef,0xc3,0xc2,0xcb,0xde,0xcd,0xd8,0xd9,0xc0,0xcd,0xd8,0xc5,0xc3,0xc2,0xdf,0x8d,0x8c,0x8c,0xf5,0xc3,0xd9,0x8c,0xc8,0xc9,0xcf,0xde,0xd5,0xdc,0xd8,0xc9,0xc8,0x8c,0xd8,0xc4,0xc9,0x8c,0xe9,0xef,0xe9,0x9f,0x94,0x9e,0x8c,0xc4,0xc5,0xc8,0xc8,0xc9,0xc2,0x8c,0xc1,0xc9,0xdf,0xdf,0xcd,0xcb,0xc9,0x8c,0xcd,0xc2,0xc8,0x8c,0xcd,0xcf,0xc4,0xc5,0xc9,0xda,0xc9,0xc8,0x8c,0xde,0xc9,0xdd,0xd9,0xc5,0xde,0xc9,0xc8,0x8c,0xca,0xd9,0xc2,0xcf,0xd8,0xc5,0xc3,0xc2,0xcd,0xc0,0xc5,0xd8,0xd5,0x8f
key:		.byte		0xac

;-------------------------------------------------------------------------------
RESET       mov.w   #__STACK_END,SP         ; Initialize stackpointer
StopWDT     mov.w   #WDTPW|WDTHOLD,&WDTCTL  ; Stop watchdog timer

length:		.equ	0x94
;-------------------------------------------------------------------------------
                                            ; Main loop here
;-------------------------------------------------------------------------------

      		mov.w	#store, R14 		;R14 is set to receive the bytes to store and string together
	 		mov.w	#ReqFxn, R12      	;load R12 with necessary info for decryptMessage here
			mov.w	#key, R13			;loads the key address into R13
			mov.w	@R13, R13			;loads the key into R13

            call    #decryptMessage

;forever:    jmp     forever

;-------------------------------------------------------------------------------
                                            ; Subroutines
;-------------------------------------------------------------------------------

;-------------------------------------------------------------------------------
;Subroutine Name: decryptMessage
;Author:
;Function: Decrypts a string of bytes and stores the result in memory.  Accepts
;           the address of the encrypted message, address of the key, and address
;           of the decrypted message (pass-by-reference).  Accepts the length of
;           the message by value.  Uses the decryptCharacter subroutine to decrypt
;           each byte of the message.  Stores theresults to the decrypted message
;           location.
;Inputs:	R6 (decrypted message)
;Outputs:	R7 (stored value)
;Registers destroyed:
;-------------------------------------------------------------------------------

decryptMessage:
			mov		#length, R15
			mov		R12, R5

loop94:
			mov.b		@R5+, R12
			call	#decryptCharacter
			mov.b	R12, 0(R14)
			inc		R14
			dec.b	R15
			jnz		loop94

			ret

;-------------------------------------------------------------------------------
;Subroutine Name: decryptCharacter
;Author:
;Function: Decrypts a byte of data by XORing it with a key byte.  Returns the
;           decrypted byte in the same register the encrypted byte was passed in.
;           Expects both the encrypted data and key to be passed by value.
;Inputs:	R5 (message) and R6 (key)
;Outputs:	R5 (decrypted message)
;Registers destroyed: ???
;-------------------------------------------------------------------------------

decryptCharacter:

			xor.b	R13, R12

            ret

;-------------------------------------------------------------------------------
;           Stack Pointer definition
;-------------------------------------------------------------------------------
            .global __STACK_END
            .sect 	.stack
;-------------------------------------------------------------------------------
;           Interrupt Vectors
;-------------------------------------------------------------------------------
            .sect   ".reset"                ; MSP430 RESET Vector
            .short  RESET
```

#Debugging

  My main.asm program has a linking error, fixed by adding GLOBAL RESET. I had a hard time trying to increment R5, which is the register holding the address, the fix was inc.b. 

#Testing/Results

  Input: 
  Output:

#Observations/Conclusions

	XOR is a valid was to decrypt a key. 

#Documentation
	Dr Coulston helped fix the linking error. Josh Hayden and Dr Coulston tried to help me fix my incrementation issue. 
