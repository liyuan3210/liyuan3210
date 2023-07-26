###### 1.chatGPT使用

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
