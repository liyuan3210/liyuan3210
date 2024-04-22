

###### 1.openai账号注册（需要国外的手机号）

1.1）首先登录openai官网（需要魔法）

登录前需要魔法，魔法后的ip要为印度，美国等国家ip

1.2）推荐使用国外邮箱注册openai账号，例如google账号

需要有一个国外手机号接收验证码，使用如下网站提供虚拟号服务来接收验证码

https://sms-activate.org/

1.3）使用googole账号注册openai账号时输入虚拟号等待验证码

收到验证码后进行注册

###### 2.chatGPT使用



```
chatgpt搭建
https://www.bilibili.com/video/BV1ao4y1g7Nz
搭建chatgpt服务
https://www.bilibili.com/video/BV1vg4y1L7wi
chatgpt调用api
https://www.bilibili.com/video/BV13h411j7Ti

潘多拉
https://github.com/pengzhile/pandora
$ docker pull pengzhile/pandora
$ docker run  -e PANDORA_CLOUD=cloud -e PANDORA_SERVER=0.0.0.0:8899 -p 8899:8899 -d pengzhile/pandora
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
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

curl https://api.openai.com/v1/chat/completions 
-H "Content-Type: application/json" 
-H "Authorization: Bearer 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" 
-d '{
"model": "gpt-3.5-turbo",
"messages": [{"role": "user", "content": "你好"}],
"temperature": 0.7
}'

docker-compose-ui工具（docker方面）
https://devpress.csdn.net/cloudnative/62f934efc6770329307fd8fc.html
```
