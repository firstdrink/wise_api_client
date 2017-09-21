# Shoppo RESTful API (for Wise)

Before getting started, please email huayang@shoppo.com to obtain an access token.

## General Setup

Shoppo production API serves at https://graphql.shoppo.com/. Please note that only SSL requests are accepted. Encoding is always UTF-8.

Test your access token with 

```bash
$ curl -s https://graphql.shoppo.com/api/wise/health_check -H "accesstoken: <Your access token>"
```

With a valid access token, you will receive a response with status code 200 like 

```json
Go Shoppo
```

Otherwise, you will receive a response with error code like below

```json
{
  "errors": [
    {
      "display_to_user": false,
      "message": "Unauthorized request",
      "type": "AccessDenied"
    }
  ]
}
```

## Endpoints

#### `/api/wise/merchant/<merchant id>`

Retrieve merchant information by id

Sample request: 
```bash
$ curl -s https://graphql.shoppo.com/api/wise/merchant/dVLgKzgew24Cx -H "accesstoken: <Your access token>"
```

Sample response:
```json
{
  "data": {
    "contact_person_name": "郭华阳",
    "contact_person_phone_number": "1860000000",
    "email": "dev@shoppo.com",
    "id": "dVLgKzgew24Cx",
    "merchant_name": "Shoppo Dev",
    "warehouse_address": "长宁区\n的一个地方",
    "warehouse_city": "上海",
    "warehouse_province": "上海",
    "warehouse_zipcode": "40102"
  }
}
```

Available fields:
- `contact_person_name (string!)`: 联系人姓名
- `contact_person_phone_number (string!)`: 联系人电话
- `email (string!)`: 商户邮箱，注：此处不一定是商户主邮箱，不建议使用
- `id (string!)`: 商户ID
- `merchant_name (string!)`: 商铺名
- `warehouse_address (string!)`: 取货地址街区
- `warehouse_city (string!)`: 取货地址城市
- `warehouse_province (string!)`: 取货地址省份
- `warehouse_zipcode (string!)`: 取货地址邮政编码

## Error codes

#### AccessDenied

The request is not authorized.

Status code: 403

Sample:

```bash
$ curl https://graphql.shoppo.com/api/wise/health_check -H "accesstoken: random_code"
{
  "errors": [
    {
      "display_to_user": false,
      "message": "Unauthorized request",
      "type": "AccessDenied"
    }
  ]
}
```

#### InvalidRelayID or (ObjectNotFoundError)

The request is attempting to retrieve an object that doesn't exist.

Status code: 404

Sample:

```bash
$ curl https://graphql.shoppo.com/api/wise/merchant/some_id -H "accesstoken: <access_token>"
{
  "errors": [
    {
      "display_to_user": false,
      "message": "Invalid relay ID some_id",
      "type": "InvalidRelayID"
    }
  ]
}
```

#### InvalidWiseMerchant

The requested merchant is missing required information.

Status code: 400

Sample:

```bash
$ curl https://graphql.shoppo.com/api/wise/merchant/dVLgKzgew24Cx -H "accesstoken: <access_token>"
{
  "errors": [
    {
      "display_to_user": false,
      "message": "The merchant has not provided warehouse address",
      "type": "InvalidWiseMerchant"
    }
  ]
}
```
