# UART Interrupt

[update] : interrupt driven uart communication is efficient. But implementing it is giving so many bugs. For the sake of time. I will stick to poll driven UART communication   

This chapter answers the question : How does the interrupt handler deal with an interrupt from the UART?    

The UART in this project can only produce 3 interrupts :
1. Data Ready Interrupt
2. THR Empty Ready Interrupt

The Handler can candle both of these interrupts.    

## 1. The THR Empty Interrupt
[undone] : documentation
rough_notes : 
- the UART gives you a THR empty interrupt by default (if it has just been initialized)
- The UART dissolves the THR empty interrupt if you read the Interrupt Status register OR you write to THR register
- 


## 2. Data Ready Interrupt
[undone] : documentation

## 3. Character Timeout

## rough notes
the Write buffer