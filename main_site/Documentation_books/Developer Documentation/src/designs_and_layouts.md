# Designs and layouts

## Birds Eye View 
![The interaction of the UART, the motherboard and peripherals](./images/UART/UART_interaction_with_CPU_keyboard_&_console.svg)  

## Keyboard Input - Level 0
![](images/UART/design/keyboard_input_lvl_0.svg)

## Keyboard Input - Level 1



```mermaid
flowchart TD
    START --> KeyBoard
    KeyBoard -->|stream of bytes| Uart_upper_interface
    Uart_upper_interface -->|Store bytes into| FIFO_Buffer
    
    subgraph UART
        FIFO_Buffer -->|check if buffer has new data| if_state{Does It?}
        if_state -->|Yes| update_interrupt_status
        if_state -->|No| Do_Nothing

        FIFO_Buffer -->|check if all Buffer data has been read| if_state_2{Has It?}
        if_state_2 -->|Yes| set_UART_as_write_ready
        if_state_2 -->|Yes| stop_read_interrupt
        if_state_2 -->|No| set_UART_as_read_ready
        if_state_2 -->|No| update_RHR_register
        update_RHR_register --> RHR_register
    end

    update_interrupt_status -->|Send read interrupt to PLIC| Plic
    Plic -->|send interrupt to CPU| CPU
    CPU -->|Summon trap handler| Trap_Handler

    subgraph Trap_handling
        Trap_Handler -->|Is the UART still read-ready?| if_state_4{Is it?}
        if_state_4 ---->|Yes| Read_from_RHR_register
        Read_from_RHR_register <--> RHR_register
        if_state_4 -->|No| Then
        Then -->|Tell Plic that interrupt has been sorted| Plic

        Trap_Handler -->|Has reading from RHR been successful?| if_state_3{Has it?}
        if_state_3 -->|Yes| store_word_into_stdin_buffer
        store_word_into_stdin_buffer --> stdin_buffer
        if_state_3 -->|No| Throw_exception
        
    end


```




