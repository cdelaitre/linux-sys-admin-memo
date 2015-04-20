# Docker API
## List images
```bash
echo -e "GET /images/json HTTP/1.0\r\n" | nc -U /var/run/docker.sock
```

```
HTTP/1.0 200 OK
Content-Type: application/json
Date: Tue, 07 Apr 2015 16:05:38 GMT

[{"Created":1428420957,"Id":"699c4816100760fbb578b3d00cac4f7ac10160991b82aedb8203851f75ad2676","ParentId":"9a9905376b14153d2383bf7874d75f228bc31f1acc6dbf2cc51b85a9dc73ca73","RepoTags":["xxx/yyy:1.0.0.8","xxx/yyy:1.0.0.8"],"Size":147470,"VirtualSize":553846735}
...
```
