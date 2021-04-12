
#curl的用法指南
curl 是常用的命令行工具，用来请求 Web 服务器。它的名字就是客户端（client）的 URL 工具的意思。

它的功能非常强大，命令行参数多达几十种。如果熟练的话，完全可以取代 Postman 这一类的图形界面工具。

不带有任何参数时，curl 就是发出 GET 请求。

`$ curl https://www.example.com`

#-A


#-具体场景

调用本地服务接口


````
curl -b 'SSO_USER_TOKEN=ss_b9EpJdin0QufvJ_BLh17zctlFIsLtw_wSER0yhkypwjzRZ9DXG6QvtBdaIKSUzeF2hYyoRS4AHREeRAqEMmRd3exrpF-QkSX9aBaQUfzLyGvXWgF61ds-wJAPzk8t0kb0TPbDvXTtrI1NJI7BMgL9sJq15wQ' -H "Content-Type: application/json" -X POST -d "{\"bindRequestDTOS\":[{\"amount\":0.1,\"bindTime\":1615185891561,\"bindUser\":\"system\",\"bindUserNick\":\"system\",\"bizCode\":\"LM20210308409509\",\"companyCode\":\"2000\",\"currency\":\"CNY\",\"customerCode\":\"TYKH201910213GPk\",\"iotWalletAccount\":\"112eb05002402000\",\"payerCustomerCode\":\"0001000055\",\"sapOrderCode\":\"1130027021\",\"transactionNumber\":\"SH20210305AE2PEIUCW740daily\"}],\"reqId\":\"7\"}" http://localhost:9436/capitalBind/v1/walletBalanceBind

````