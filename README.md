




# 개인과제 Capture List


- 평가항목

  -eventstorming
  
  ![image](https://user-images.githubusercontent.com/68408645/92618935-d86da980-f2fb-11ea-9923-5f374a20b0de.png)

  
  - SAGA:  


    ![image](https://user-images.githubusercontent.com/68408645/92605283-840efd80-f2ec-11ea-987f-87f8d724d94b.png)

  
    ![image](https://user-images.githubusercontent.com/68408645/92604843-f206f500-f2eb-11ea-816e-67ab1becf894.png)

  
  

  - [local] CQRS(Mypage view): 
  
  
  ![image](https://user-images.githubusercontent.com/68408645/92607076-9d18ae00-f2ee-11ea-9a7f-f071a94100ca.png)


  ![image](https://user-images.githubusercontent.com/68408645/92606248-b5d49400-f2ed-11ea-8d1a-901206300ec2.png)


  ![image](https://user-images.githubusercontent.com/68408645/92606906-75294a80-f2ee-11ea-95a1-88fc956f8963.png)

  

  
  - [local] req/resp : 
  
  ```
  @PostPersist
    public void onPostPersist(){
        Bought bought = new Bought();
        BeanUtils.copyProperties(this, bought);
        bought.publishAfterCommit();

        //Following code causes dependency to external APIs
        // it is NOT A GOOD PRACTICE. instead, Event-Policy mapping is recommended.

        car_order.external.Delivery delivery =  new car_order.external.Delivery();

        //동기 호출의 중요한 방식.
        delivery.setOrderId(this.getOrderId());
        delivery.setStatus("shipped");
        delivery.setPurchaseId(this.getId());

        // mappings goes here
        PurchaseApplication.applicationContext.getBean(car_order.external.DeliveryService.class)
            .ship(delivery);

    }
  ```
  
  - [local] Circuit Breaker 
  
  
  
  - Deploy/Pipeline
  
  
  
  - Autoscale (HPA)
  
  
  
  - Self-healing (Liveness Probe)
  
  
  
  - Zero-downtime deploy (Readiness Probe)
  






