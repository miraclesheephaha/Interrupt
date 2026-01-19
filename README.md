# Interrupt Interface
* The PCH support both APIC and PIC modes.
> 1. 要求中斷控制器必須追蹤來自各個中斷源的(Assert/De-assert)下降/上升sideband message。透過訊息中的source ID進行解碼實現功能。  
> 2. 小於24的IRQ向下兼容以前的版本支援*Interrupt sharing*  
> 3. IRQ24 119是專用(Dedicated)且不可共享的，不允許將一個以上的中斷發起者(Interrupt Initiator)，分配給同一個IRQ編號  
> 4. 映射到IRQ24 119的潛在多誘因中斷發起者(Multi-cause interrupt initiators)包含GPIO, eSPI等裝置。

# 為何需interrupt?  
> 電腦周邊設備速度比cpu速度慢，如果讓cpu一直訪問device是否需處理(polling)，會沒效率，所以採取當設備有需要服務時，由設備端去通知cpu，cpu收到資訊後，中斷當前工作，處理設備需求，之後再回原先cpu的任務

# interrupt兩種模式  
PIC Mode(8259 Mode) and IO APIC Mode  

## 8259 mode流程  
* 第一階段：初始化
在device發送中斷前，OS需先讓8259知道如何運作，需透過ICW(Initialization Command Word)完成。
1. ICW1：設定cascade模式或single模式
2. ICW2：中斷vector對應，決定硬體中斷IRQ是多少?且每個IRQ有對應的vector，cpu會拿這些vector查詢需做那些對應動作。
3. ICW3：告訴Master PIC IRQ2有接slave PIC
4. ICW4：設定AEOI(自動結束中斷)，設定架構8086 intel架構

*　第二階段：中斷請求與遮罩(Requesst & Mask)  
5. Device例如鍵盤拉高電位，拉高IRQ1之類的  
6. 

