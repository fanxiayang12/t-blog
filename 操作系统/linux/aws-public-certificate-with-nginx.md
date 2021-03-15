# How to set up SSL with AWS certificate manager

## [Answered by stackoverflow](https://stackoverflow.com/questions/61502474/adding-aws-public-certificate-with-nginx)
    ACM can generate and manage SSL/TLS certificates for AWS based websites and applications.We can create or import and then manage a certificate using ACM.
    However,an ACM certificate can only be created with services,such as Elastic Load Balancing.

    This is because one cannot use ACM to directly install the certificate on AWS-based website or application.

    Before we install the SSL certificate,we should have a project,served by NGINX behind a load balancer.

    You can attach certificates issued with ACM to the AWS Load balancer and hide your instance behind the load balancer.

## [ACM with load balancer](https://bobcares.com/blog/aws-certificate-manager-nginx/)

## Manage SSL directly

issue CA with another tool

[letsencrypt](https://letsencrypt.org/)
[certbox](https://certbot.eff.org/lets-encrypt/centosrhel7-nginx)

### [Ubuntu](https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/)
### [Amazon linux 2](https://aws.amazon.com/blogs/compute/extending-amazon-linux-2-with-epel-and-lets-encrypt/)

    LAMP
        Linux/Apache/MariaDB/PHP stack
        one of the most popular platforms for deploying websites
    EPEL
        Extra Packaged for Enterprise Linux collection
### 实战记录
    [HTTPS采坑记录](https://allenmind.cn/给小站弄HTTPS的踩坑过程/)

### 有效
```shell
sudo certbot --nginx -d www.spaclens.com --nginx-server-root /usr/local/nginx --nginx-ctl /usr/local/nginx/sbin/nginx
```