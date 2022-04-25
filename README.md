# parse-usdl

parse Pdf417 barcode data from US driver licenses

## chorpler fork

- Forked and updated, 2022-04-24
- Old version was throwing "key not found" errors
- Added a debug option if you want to see what line it is having trouble with
- Updated documentation

## Usage

```js
import { parse } from 'parse-usdl'

const code = `@

ANSI 636001070002DL00410392ZN04330047DLDCANONE
DCBNONE
DCDNONE
DBA08312013
DCSMichael
DACM
DADMotorist
DBD08312013
DBB08312013
DBC1
DAYBRO
DAU064 in
DAG2345 ANYWHERE STREET
DAIYOUR CITY
DAJNY
DAK123450000
DAQNONE
DCFNONE
DCGUSA
DDEN
DDFN
DDGN
`
const data = parse(code)
console.log(JSON.stringify(data, null, 2))

// {
//   "jurisdictionRestrictionCodes": "NONE",
//   "jurisdictionEndorsementCodes": "NONE",
//   "dateOfExpiry": 1377921600000,
//   "lastName": "Michael",
//   "firstName": "M",
//   "middleName": "Motorist",
//   "dateOfIssue": 1377921600000,
//   "dateOfBirth": 1377921600000,
//   "sex": "M",
//   "eyeColor": "BRO",
//   "height": "064 in",
//   "addressStreet": "2345 ANYWHERE STREET",
//   "addressCity": "YOUR CITY",
//   "addressState": "NY",
//   "addressPostalCode": "123450000",
//   "documentNumber": "NONE",
//   "documentDiscriminator": "NONE",
//   "issuer": "USA",
//   "lastNameTruncated": "N",
//   "firstNameTruncated": "N",
//   "middleNameTruncated": "N"
// }
//
```

## Optional Parameters

#### suppressErrors

Prevent a hard error in the case of a bad code provided. All valid codes will be parsed and returned.

#### debug

Set to `true` if you want to get console log debugging output on a per-line basis.

```javascript
const options = {
  suppressErrors: false,
  debug: false,
}
```

## References

- [AAMVA DL/ID Card Design Standard (2020)](https://www.aamva.org/assets/best-practices-guidance/dl-id-card-design-standard)
  - This is the standard defining the encoded data this package is intended to parse.
- [AAMVA 2020 North American Standard - Personal Identification](https://www.aamva.org/getmedia/99ac7057-0f4d-4461-b0a2-3a5532e1b35c/AAMVA-2020-DLID-Card-Design-Standard.pdf)
  - The actual 125-page PDF document defining the standard
- [AAMVA list of Issuer Identification Numbers](<https://www.aamva.org/identity/issuer-identification-numbers-(iin)>)
  - A list of the identification numbers for _Issuers_ (also known as IINs) recognized by the standard. These are the entities with authority to issue official ID using this standard.
  - The raw PDF417 value of a license will include near the beginning a line like this:
    - `ANSI 636001070002DL00410392ZN04330047DLDCANONE`
    - The first 6 digits after the `ANSI` are the IIN. In this example, the IIN is `636001` which means the ID was issued by `New York`.

## Unanswered Questions

- After the actual ANSI/AAMVA data fields, there is often one or more additional lines that are proprietary to the issuer.
  - Some of these start with `Z`
  - These are presumably checksums, hashes, or encrypted values of some kind to help prevent counterfeiting
  - When decoded, one of the New York DMV's [sample **Real ID** licenses](https://dmv.ny.gov/id-card/sample-photo-documents) says:
    ```
    ZNZNAMOTORIST
    ZNBENCRYPTED ELEMENT GOES HERE
    ```
