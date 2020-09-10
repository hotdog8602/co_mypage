




# 개인과제 Capture List


- 평가항목

  -eventstorming
  
  ![image](https://user-images.githubusercontent.com/68408645/92618935-d86da980-f2fb-11ea-9923-5f374a20b0de.png)

  
  - SAGA:  

```
@StreamListener(KafkaProcessor.INPUT)
    public void wheneverOrdered_Buy(@Payload Ordered ordered){

        if(ordered.isMe()){
            System.out.println("##### listener ordered : " + ordered.toJson());

            Purchase purchase = new Purchase();
            purchase.setOrderId(ordered.getId());
            purchase.setQty(ordered.getQty());
            purchase.setStatus("Purchased.");

            purchaseRepository.save(purchase);


        }
    }
    
```


   ![image](https://user-images.githubusercontent.com/68408645/92605283-840efd80-f2ec-11ea-987f-87f8d724d94b.png)

  
   ![image](https://user-images.githubusercontent.com/68408645/92604843-f206f500-f2eb-11ea-816e-67ab1becf894.png)


- purchase 서비스가 죽어도 정상 서비스 됨(장애격리)

  ![image](https://user-images.githubusercontent.com/68408645/92624248-3ef5c600-f302-11ea-9a0d-a04ff0b7233c.png)


  ![image](https://user-images.githubusercontent.com/68408645/92623950-eb837800-f301-11ea-8bf3-1e77502e0950.png)

  

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
  
  - delivery 서비스가 죽으니 장애가 전파됨.
  
  ![image](https://user-images.githubusercontent.com/68408645/92624436-78c6cc80-f302-11ea-8aa6-c7962b06788a.png)

  
  ![image](https://user-images.githubusercontent.com/68408645/92623633-76b03e00-f301-11ea-8ade-274249872af9.png)

  
  
  
  - [local] Circuit Breaker 
  
``` 
  feign:
  hystrix:
    enabled: true

hystrix:
  command:
    default:
      execution.isolation.thread.timeoutInMilliseconds: 610
   ```
  
  
  ```   
    @PostPersist
    public void onPostPersist(){

        try {
            Thread.currentThread().sleep((long) (400 + Math.random() * 220));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        Shipped shipped = new Shipped();
        BeanUtils.copyProperties(this, shipped);
        shipped.publishAfterCommit();
 
    }
  
  ```
  
  ![image](https://user-images.githubusercontent.com/68408645/92623029-b165a680-f300-11ea-96ce-2412ea5c728f.png)

  
  - Deploy/Pipeline
  
  
  ![image](https://user-images.githubusercontent.com/68408645/92672245-324d8e00-f353-11ea-96eb-16739fd36479.png)
  
  
  ![image](https://user-images.githubusercontent.com/68408645/92672155-09c59400-f353-11ea-9607-7b55196ed467.png)

  
  - CI/CD 이후 Cloud에서 조회한 결과
  
  ![image](https://user-images.githubusercontent.com/68408645/92671634-c0c11000-f351-11ea-9119-ba4f5d1e76e8.png)

  
  
  - Autoscale (HPA)
  
  
  
  - Self-healing (Liveness Probe)
  
  
  
  - Zero-downtime deploy (Readiness Probe)
  






