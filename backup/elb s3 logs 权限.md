设置s3的权限https://docs.aws.amazon.com/elasticloadbalancing/latest/application/enable-access-logging.html

该策略向指定的 Elastic Load Balancing 账户 ID 授予权限。将此策略用于以下所列 区域的可用区或 Local Zones 中的负载均衡器。

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::elb-account-id:root"
      },
      "Action": "s3:PutObject",
      "Resource": "s3-bucket-arn"
    }
  ]
}
```

将 `elb-account-id` 替换为用于所在区域 Elastic Load Balancing 的 AWS 账户 的 ID：

- 美国东部（弗吉尼亚州北部）– 127311923021
- 美国东部（俄亥俄州）– 033677994240
- 美国西部（北加利福尼亚）– 027434742980
- 美国西部（俄勒冈州）– 797873946194
- 非洲（开普敦）– 098369216593
- 亚太地区（香港）– 754344448648
- 亚太地区（雅加达）– 589379963580
- 亚太地区（孟买）– 718504428378
- 亚太地区（大阪）– 383597477331
- 亚太地区（首尔）– 600734575887
- 亚太地区（新加坡）– 114774131450
- 亚太地区（悉尼）– 783225319266
- 亚太地区（东京）– 582318560864
- 加拿大（中部）– 985666609251
- 欧洲（法兰克福）– 054676820928
- 欧洲（爱尔兰）– 156460612806
- 欧洲（伦敦）– 652711504416
- 欧洲（米兰）– 635631232127
- 欧洲（巴黎）– 009996457667
- 欧洲（斯德哥尔摩）– 897822967062
- 中东（巴林）– 076674570225
- 南美洲（圣保罗）– 507241528517

用您的访问日志所在位置的 ARN 替换 `s3-bucket-arn`。您指定的 ARN 取决于您是否计划在[[步骤 3](https://docs.aws.amazon.com/zh_cn/elasticloadbalancing/latest/application/enable-access-logging.html#enable-access-logs)](https://docs.aws.amazon.com/zh_cn/elasticloadbalancing/latest/application/enable-access-logging.html#enable-access-logs) 中启用访问日志时包含前缀.