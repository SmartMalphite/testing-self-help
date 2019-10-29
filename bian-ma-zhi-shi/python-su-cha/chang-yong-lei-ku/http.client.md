# http.client

### http.client

```text
import http.client

url = "xxx.xxx.com"
headers = {
    'Content-Type': "application/json",
    'cache-control': "no-cache"
}
payload = json.dumps(data_dict)

conn = http.client.HTTPSConnection(url)
conn.request("POST", "/pay/barcode/query", payload, headers)
response = conn.getresponse()

print(response.status, response.reason)
print(response.read())
```

