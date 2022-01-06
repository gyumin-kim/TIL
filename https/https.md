# HTTPS

## AWS를 활용하여 간단하게 HTTPS를 적용하는 방법
1. EC2에 Load Balancer(ELB)를 연결한다.
2. [ACM](https://aws.amazon.com/ko/certificate-manager/)(AWS Certificate Manager)에서 SSL(TLS) 인증서를 생성하고, LB에 인증서를 연결한다.
    - https://aws.amazon.com/ko/premiumsupport/knowledge-center/associate-acm-certificate-alb-nlb/
- 참고 링크
    - https://happiestmemories.tistory.com/48
    - https://perfectacle.github.io/2017/10/05/https-with-elb/
        - (EC2에 nginx 설치 필요)