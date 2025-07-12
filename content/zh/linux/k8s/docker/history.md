---
title: history
description: history
date: 2023-10-26
weight: 20000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- [docker](https://docs.docker.com/reference/cli/docker/)

{{< /alert >}}



`2024-08-05T09:25:11 [root@node70 /root/tmp/greatrds-api] $ docker history  --no-trunc  b592fbf3326b`
## 8字段
```bash
IMAGE                                                                     CREATED       CREATED BY                                                                                                                                                                                                SIZE      COMMENT
sha256:b592fbf3326b333701ae1ee5e47960f19e72035f1f50f9d8be0842b5b94d8196   4 weeks ago   /bin/sh -c #(nop)  CMD ["run_api"]                                                                                                                                                                        0B
sha256:490f2342689c521d057286b5cf1fecf698808493671dd4c3b8c8e99e34b95301   4 weeks ago   /bin/sh -c #(nop)  ENTRYPOINT ["./docker-entrypoint.sh"]                                                                                                                                                  0B
sha256:cd4303d5415050a6068290e9298b47467954c14af4d5b0c1f2ba895632e89339   4 weeks ago   /bin/sh -c chmod +x docker-entrypoint.sh                                                                                                                                                                  1.04kB
sha256:c493251a96d8b48cf3540500437edd8347dca05d59a363585a6834bfc94ae9e8   4 weeks ago   /bin/sh -c #(nop) ADD file:e8ae9d013f744a4197408795374ac5838cc3790c64db206bb3a05690908b13d9 in .                                                                                                          1.04kB
sha256:6ef0daebefae9e16862d0a5783c8ecec7920d5b4272c056e35cea0c56b11d4cb   4 weeks ago   /bin/sh -c #(nop) COPY file:1d0c5c02a1de85b6b5af2467209d0c4ecf819de9f8e62adc5d723972605a1611 in /usr/local/etc/greatrds/api-paste.ini                                                                     1.74kB
sha256:575705a4c38827ff6eb52e697047f59dde17fb14bdbb7353bb406f896207d5cb   4 weeks ago   /bin/sh -c #(nop)  ENV GREATRDS_LOCALEDIR=/usr/local/lib/python3.6/site-packages/greatrds/locale                                                                                                          0B
sha256:aac043c197d23bb881030c022c4647c56b3a8c069ba2caab78b68f7f447406a4   4 weeks ago   /bin/sh -c pip install -q greatrds -i http://172.17.134.70:18080/simple  --trusted-host 172.17.134.70                                                                                                     289MB
sha256:95f5273cf07200d66bea166f9dbb36b3c2f29ee601ba71e22013f4647e11593c   3 years ago   /bin/sh -c chown mysql:mysql /usr/bin/pt-query-digest                                                                                                                                                     528kB
<missing>                                                                 3 years ago   /bin/sh -c chmod +x /usr/bin/pt-query-digest                                                                                                                                                              528kB
<missing>                                                                 3 years ago   /bin/sh -c #(nop) COPY file:d7c36cb108d34b7b21d03320eb6be0f88d5608ca9b07a32b2f9afba72f2e9577 in /usr/bin/pt-query-digest                                                                                  528kB
<missing>                                                                 3 years ago   /bin/sh -c yum install -y mysql                                                                                                                                                                           146MB
<missing>                                                                 3 years ago   /bin/sh -c useradd mysql                                                                                                                                                                                  296kB
<missing>                                                                 3 years ago   /bin/sh -c #(nop) WORKDIR /root                                                                                                                                                                           0B
<missing>                                                                 3 years ago   /bin/sh -c pip3 install gunicorn==19.9 -i https://pypi.tuna.tsinghua.edu.cn/simple                                                                                                                        904kB
<missing>                                                                 3 years ago   /bin/sh -c pip3 install -U pip -i https://pypi.tuna.tsinghua.edu.cn/simple                                                                                                                                10.5MB
<missing>                                                                 3 years ago   /bin/sh -c yum install -y perl 'perl(Data::Dumper)' perl-Digest-MD5 perl-DBI perl-DBD-MySQL perl-Time-HiRes perl-IO-Socket-SSL                                                                            106MB
<missing>                                                                 3 years ago   /bin/sh -c yum install -y make git python3 python3-devel python3-pip gcc screen nmap-ncat numactl socat net-tools which                                                                                   275MB
<missing>                                                                 3 years ago   /bin/sh -c #(nop)  ENV LC_CTYPE=en_US.UTF-8                                                                                                                                                               0B
<missing>                                                                 3 years ago   /bin/sh -c #(nop)  ENV LC_COLLATE=C                                                                                                                                                                       0B
<missing>                                                                 3 years ago   /bin/sh -c #(nop)  ENV LANGUAGE=en_US.UTF-8                                                                                                                                                               0B
<missing>                                                                 3 years ago   /bin/sh -c #(nop)  ENV LANG=en_US.UTF-8                                                                                                                                                                   0B
<missing>                                                                 5 years ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]                                                                                                                                                                      0B
<missing>                                                                 5 years ago   /bin/sh -c #(nop)  LABEL org.label-schema.schema-version=1.0 org.label-schema.name=CentOS Base Image org.label-schema.vendor=CentOS org.label-schema.license=GPLv2 org.label-schema.build-date=20181204   0B
<missing>                                                                 5 years ago   /bin/sh -c #(nop) ADD file:54b004357379717dfb7ea6f024ca80ce762ea4b06647fcddc0f6697146551172 in /                                                                                                          202MB
2024-08-05T09:25:52 [root@node70 /root/tmp/greatrds-api] $

```












