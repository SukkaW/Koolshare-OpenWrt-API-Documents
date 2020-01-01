# Koolshare-OpenWrt-API-Documents

The API documents (Unofficial) for Koolshare OpenWrt httpdb.

## License and Disclaimer

**Koolshare-OpenWrt-API-Documents** is released under the MIT License - see the [LICENSE](./LICENSE) file for details.

Koolshare-OpenWrt-API-Documents itself and its author is not related with Koolshare OpenWrt, `koolshare.cn` website, or Koolshare Dev Team.<br>
Koolshare-OpenWrt-API-Documents and its author is not responsible for any loss of any koolshare plugin developer, including but not limited to Kernel Panic, device fail to boot or can not function normally, storage damage or data loss, atomic bombing, World War III, The CK-Class Restructuring Scenario that [SCP Foundation](http://www.scp-wiki.net/) can not prevent, and so on.

## Store Data / Execute Shell

Store data into `skipdb` k-v database, execute a shell script (under `/koolshare/scripts/`)

**Endpoint**

`POST /_api`

**Request Parameters**

| Name / type | Description / example | Constraints |
|:---|:---|:---:|
| `id` / Number | The request ID / `114514` | Required |
| `method` / String | The shell script to be executed / `"koolclash_update_ipdb.sh"` | Required* |
| `params` / Array | The parameters to be passed to the shell script / `['Hello', 1919810]`** | Optional |
| `fields` / Object | The data to be stored into k-v database / `{ "key1": "value1", key2 : "value2" }` | Optional |

*: Which means you just can not store something into k-v database without executing any shell scripts
**: The `params` passed to the shell script started from `$2`. From the example value given, the actual execution is:

```bash
$ /koolshare/scripts/koolclash_update_ipdb.sh "[id]" "[params[0]]" "[params[1]]" ...
```

**Response**

| Name | Description |
|:---|:---|
| `result` | The result of the shell script*, or the error code** |

*: The executed shell script must call `http_response [result]` to response. If there is no `http_response` in the shell script, the request just simply timeout. If there are muiltiple `http_response`, only the first will be responsed, and the request ends.
**: `-2` means the shell script is not found. `-3` means missing required request parameters.

**Sample Call**

```js
const id = parseInt(Math.random() * 100000000);

fetch(`/_api?_=${Date.now()}`, {
    method: 'POST',
    cache: 'no-cache',
    headers: {
        'Content-Type': 'application/json'
        // 'Content-Type': 'application/x-www-form-urlencoded' is also allowed
    },
    redirect: 'follow',
    body: JSON.stringify({
        id,
        method: 'koolclash_update_ipdb.sh',
        params: ['del', 114514],
        fields: {
            key1: 'value1',
            key2: 'value2'
        }
    })
})
```

**Example Response**

```
200 OK

{ result: "ok" }
```

## Get Data from `skipdb` K-V Database

Get stored data with prefix matched key from `skipdb` k-v database.

**Endpoint**

`GET /_api/:identifier`*

*: All data with `:identifier` prefixed key will be returned.

**Response**

| Name | Description |
|:---|:---|
| `result` | The key-value data requested, or the error code* |

*: `-4` means no `:identifier` is given during the request.

**Sample Call**

```js
fetch(`/_api/h?_=${Date.now()}`, {
    cache: 'no-cache',
    headers: {
        'Content-Type': 'application/json'
        // 'Content-Type': 'application/x-www-form-urlencoded' is also allowed
    },
    redirect: 'follow'
})
```

**Example Response**

```
200 OK

{
    result: [{
        "hello": "world",
        "hi": "koolclash"
    }]
}
```

## Get the Return Value

Get the return value of the executing shell script.

**Endpoint**

`GET /_result/:id`*

*: The request id used when calling the shell script

**Response**

| Name | Description |
|:---|:---|
| `result` | The return value, or the error code* |

*: `-1` The request is not found.

```js
fetch(`/_result/${reqId}`, {
    cache: 'no-cache',
    redirect: 'follow'
})
```

**Example Response**

```
200 OK

{ result: 0 }
```

## Temporarily Set the Return Value

Set the result of the shell script for 1 minutes.

**Endpoint**

`POST /_resp/:id`*

*: The request id used when calling the shell script

**Response**

| Code | Description |
|:---:|:---|
| `0` | The return value set successfully |
| `-1` | The request is not found |

**Sample Call**

```js
fetch(`/_resp/${reqId}`, {
    method: 'POST',
    cache: 'no-cache',
    redirect: 'follow',
    body: 'hello'
})
```

**Example Response**

```
200 OK

{ result: 0 }
```

## File Uploading

Upload the file to `/tmp/upload/`.

**Endpoint**

`POST /_upload`

**Response**

| Name | Description |
|:---|:---|
| `filepath` | The path prefixed with `/tmp/upload/` |
| `filelength` | The size of the file in Byte |

**Sample Call**

```js
const formData = new FormData();
const fileField = document.querySelector("input[type='file']");

formData.append('_config.yml', fileField.files[0]);

fetch(`/_upload`, {
    method: 'POST',
    cache: 'no-cache',
    body: formData
})
```

**Example Response**

```
200 OK

{ filepath: "/tmp/upload/_config.yml", filelength: 114514 }
```

## Directory Listing

List the `/koolshare/webs/` directory. [Mongoose](https://github.com/cesanta/mongoose) web server will be used.

## Author

**Koolshare-OpenWrt-API-Documents** © [Sukka](https://github.com/SukkaW), Released under the [MIT](./LICENSE) License.<br>
Authored and maintained by [Sukka](https://github.com/SukkaW).

> [Personal Website](https://skk.moe) · [Blog](https://blog.skk.moe) · GitHub [@SukkaW](https://github.com/SukkaW) · Telegram Channel [@SukkaChannel](https://t.me/SukkaChannel) · Twitter [@isukkaw](https://twitter.com/isukkaw) · Keybase [@sukka](https://keybase.io/sukka)
