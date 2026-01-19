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
   
* 第二階段：中斷請求與遮罩(Requesst & Mask)    
6. Device例如鍵盤拉高電位，拉高IRQ1之類的  
7. OCW1 Mask檢查：8259檢查內部IMR(Interrupt Mask Register)。由OS透過OCW1事先設好，設0開啟IRQE中斷1為關閉  
8. IRR紀錄：把要中斷的設備IRQ紀錄在IRR等待執行  

* 第三階段：中斷承認週期(Acknowledge cycle)
  
8. 發出INTR：8259拉高連往CPU的INTR引腳
9. 第一次INTA(凍結)，CPU回應第一個INTA訊號，8259收到後，立刻凍結IRR狀態，並在內部進行優先權判定，選出最高要求
   > 為何8259 MODE會需要凍結而IO APIC不用Acknowledge cycle?
   > 因為8259他是走實體線路，且只有一條通道可與cpu溝通，凍結的原因是怕電位不穩導致不準，8259內部的優先權判定是一套複雜的邏輯閘，如果IRQ3剛觸發，電路正在運算優先權突然IRQ1跳出，會導致電路的電位不穩
   > IO APIC是虛擬線路，假如4核心CPU會有4個local apic每個會有256個位元對應0~255 vector，os寫入IO APIC的RTE可存放多組IRQ設備需求，且優先權是由算法計算可在瞬間比較完成。  
10.  

