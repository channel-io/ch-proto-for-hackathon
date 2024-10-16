## ch-proto-for-hackathon

채널톡 app store native function 관련 gRPC proto

Protocol Buffers(약칭 protobuf)는 구조화된 데이터를 직렬화하기 위한 구글의 언어 중립적이고 플랫폼 중립적이며 확장 가능한 메커니즘입니다.
자세한 내용은 [Protocol Buffers 문서](https://protobuf.dev)에서 확인할 수 있습니다.

.proto 파일들을 통해 app store 서버에 어떤 형식, 타입으로 native function 요청을 보내야 하는지, 어떤 응답이 돌아오는지 알 수 있습니다.

예를 들면, Get User 요청을 할 때, service/user.proto의 GetUserRequest를 보면 parameter를 어떻게 구성해야 하는지 알 수 있습니다.
```
// service/user.proto
message GetUserRequest {
  string channel_id = 2 [(buf.validate.field).required = true];
  string user_id = 1 [(buf.validate.field).required = true];
}


// request body
request body {
  "method": "getUser",
  "params": {
    "channelId": "string",
    "userId": "string"
  }
}
```
위에서 볼 수 있듯이 channel id와 user id를 string 형식으로 전달하면 됩니다. protobuf에서는 snake case로 변수 이름을 작성하지만 app store 서버에서는 camel case로 입력을 받기 때문에 "channelId", "userId"로 request를 구성해야합니다.

이 때 돌아오는 응답은 아래와 같습니다.
```
// service/user.proto
message GetUserResult {
  io.channel.coreapi.v1.model.User user = 1;
}

// model/user.proto
message User {
  string id = 1;
  string channel_id = 2;
  string member_id = 3;
  ...
  google.protobuf.Timestamp created_at = 32;
  google.protobuf.Timestamp updated_at = 33;
  int64 version = 34;
}


// response body
response body {
  "result": {
    "user": {
      "id" : "...",
      "channelId" : "...",
      "memberId" : "...",
      ...
      "createdAt" : ...,
      "updatedAt" : ...,
      "version" : ...
    }
  }
}
```
