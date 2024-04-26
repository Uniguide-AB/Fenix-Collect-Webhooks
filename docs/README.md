# About Fenix Collect Webhooks



### Process

1. A contact conversion is completed by the prospect *(agreement has been signed with BankId)*.
2. Fenix Collect will send a payload to the registered URL(s) for the customer.
2. Each webhook request assumes a status code of 2xx to be returned.


### The Payload

They payload is JSON and we are able to provide custom headers with the payload.


#### The body

| **Field**              | **Type**                      | **Remarks**                           |
| ---------------------- | ----------------------------- | ------------------------------------- |
| `payload_generated_at` | `string`                      | ISO 8601. When the body was generated |
| `campaign`             | [campaign](#campaign)         | Resource                              |
| `contact`              | [contact](#contact)           | Resource                              |
| `bank_details`         | [bank_details](#bank_details) | Resource                              |


#### <a name="campaign"></a> [campaign](#campaign)  

| Field           | Type     | Remarks               |
| --------------- | -------- | --------------------- |
| `uuid`          | `string` | UUID V4               |
| `name`          | `string` | Name for public view  |
| `internal_name` | `string` | Name for internal use |

#### <a name="contact"></a> [contact](#contact)  

| Field         | Type                  | Remarks                                                      |
| ------------- | --------------------- | ------------------------------------------------------------ |
| `uuid`        | `string`              | UUID V4                                                      |
| `name`        | `string`              | Name of contact                                              |
| `last_name`   |`string` `(nullable)`  | Last name of contact (campaign setting)                      |
| `ssn`         | `string`              | Social security number, `YYYYMMDDXXXX`                       |
| `external_id` | `string` `(nullable)` | The customers CRM system ID. Manually inserted by Fenix Collect users and is not guaranteed to be correct |
| `phone`       | `string` `(nullable)` |                                                              |
| `email`       | `string` `(nullable)` |                                                              |
| `created_at`  | `string`              | ISO 8601. When the contact was created in the database, not when it was converted |

#### <a name="bank_details"></a> [bank_details](#bank_details)  

| Field            | Type                  | Remarks                                                      |
| ---------------- | --------------------- | ------------------------------------------------------------ |
| `name`           | `string`              | UUID V4                                                      |
| `clearing`       | `string`              | Named aimed towards prospect                                 |
| `account`        | `string`              | Social security number, `YYYYMMDDXXXX`                       |
| `amount_to_give` | `string` `numeric`    | No fractions                                                 |
| `frequency`      | `string` `(nullable)` | free text. How often money may be withdrawn, if null the default should be considered monthly. |

#### Payload example
```json
{
  "payload_generated_at": "2023-11-08T22:31:13+01:00",
  "campaign": {
    "uuid": "9a9114a5-7fa7-4ef3-be62-48bc7599d3f0",
    "name": "Fånga våren tillsammans med oss",
    "internal_name": "TM_VK_2021_V4"
  },
  "contact": {
    "uuid": "9a9114a5-83df-48b0-9306-40d744e65cc0",
    "name": "My Möller",
    "last_name": null,
    "ssn": "196702107787",
    "external_id": "1234567",
    "phone": "+16466637874",
    "email": "qrunolfsdottir@schultz.net",
    "created_at": "2023-11-08T22:31:13+01:00"
  },
  "bank_details": {
    "name": "SEB",
    "clearing": "12345",
    "account": "12334346657",
    "amount_to_give": "100",
    "frequency": "Månadsvis"
  }
}
```



### Backoff / retries

If the original webhooks fails, a  total of 5 retries will occur automatically, then it will be marked as failed and a headsup is sent to Uniguide.

There retries will also backoff after each unique fail and endpoint.

1. 1 min
2. 5 min
3. 15 min
4. 60 min
5. 720 min _(12 hours)_
