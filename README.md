# Interrupt Interface
* The PCH support both APIC and PIC modes.
> 要求中斷控制器必須追蹤來自各個中斷源的(Assert/De-assert)下降/上升sideband message。透過訊息中的source ID進行解碼實現功能。
> 小於24的IRQ向下兼容以前的版本支援*Interrupt sharing*
> IRQ24 119是專用(Dedicated)且不可共享的，不允許將一個以上的中斷發起者(Interrupt Initiator)，分配給同一個IRQ編號
> 映射到IRQ24 119的潛在多誘因中斷發起者(Multi-cause interrupt initiators)包含GPIO, eSPI等裝置。
