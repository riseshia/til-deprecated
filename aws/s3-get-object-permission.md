# S3 permission

`s3:GetObject`은 해당하는 키를 찾지 못할 경우의 동작이 사용자가 가지고 있는 권한에 따라서 달라지는데,

- s3:ListBucket이 있음 -> 404 NoSuchKey
- s3:ListBucket이 없음 -> 403 AccessDenied

이게 도대체 뭔가 싶었는데, 그도 당연한게, 해당하는 키가 존재하지 않는다는 것을 사용자가 알려면 해당하는 버킷의 목록을 알고 있을 필요가 있음.

비유하자면, 로그인 할 경우에 틀린 아이디를 입력하면 '해당하는 아이디는 존재하지 않습니다'가 아닌 '로그인에 실패했습니다'를 돌려주는 것과 비슷한 경우.

Ref: http://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html
