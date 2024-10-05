## properties 파일


    spring.application.name=payday
    
    spring.datasource.driver-class-name =com.mysql.cj.jdbc.Driver
    spring.datasource.url=jdbc:mysql://localhost:3306/PayDayDB?createDatabaseIfNotExist=true
    spring.datasource.username=${DB_USERNAME:Mysql DB이름}
    spring.datasource.password=${DB_PASSWORD:비밀번호}
    spring.jpa.hibernate.ddl-auto=update



## JPA persistance (caching) 적용된 object를 사용하여 2차 변경 사항을 적용하는 코드
        @Transactional
        @Override
        public Receipt updateReceiptContentsById(Long receiptId, Receipt receipt) {
            Optional<Receipt> qs = receiptRepository.findById(receiptId);
            if (qs.isPresent()) {
                Receipt p = qs.get();
    
                //List Clear
                p.getReceiptContents().clear();
                //orphan DropTable : receiptContent
                Receipt flushed = receiptRepository.save(p);
    
    
                List<ReceiptContent> data = receipt.getReceiptContents();
    
                for (ReceiptContent res : data) {
                    flushed.getReceiptContents().add(res);
                    res.setReceipt(flushed);
                }
                return receiptRepository.save(flushed);
    
    
            } else {
                throw new RuntimeException("none");
            }
    
        }
    
    }

## json reference를 적절히 조절하여, 3중 연관구조에서 데이터를 원하는 레이어만큼 꺼내옴
이번엔 entity graph와 dto 안쓰고 해봄.
