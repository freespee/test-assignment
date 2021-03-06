# Technical test for Test lead

## Test scenario

![](https://i.imgur.com/uI5FuQP.png)

### General

- The individual services are separate NodeJS applications
- `AUTH SERVICE` is being used by multiple other services, it is responsible for validating API keys and translating those into JWT that are used internally between other services. The JWT tokens can be validated autonomously by the services themselves.
- `DATA SERVICE` is being used by multiple other services, it is responsible for fetching data from databases and other relevant places (such as Redis, Logs etc).
- `API KEY` is used as authentication in the public facing service (`API SERVICE`)
- `JWT TOKEN` is used as authentication in the internal services (`CALCULATION SERVICE`, `DATA SERVICE`, `EMAIL SERVICE`)

### Flow

- Users can call the `API SERVICE` (with an API KEY) to send an email report
- The `API SERVICE` fetches a JWT token for internal use thereby validating the given API KEY
- The `API SERVICE` then calls the `CALCULATION SERVICE` with the parameters from the user's request
- The `CALCULATION SERVICE` fetches data from the `DATA SERVICE` depending on the given parameters
- The `CALCULATION SERVICE` performs calculations on the data and calls the `EMAIL SERVICE` with the REPORT DATA
- The `EMAIL SERVICE` takes the REPORT DATA and builds up an HTML email which it sends to the `THIRD PARTY EMAIL PROVIDER`
- The `THIRD PARTY EMAIL PROVIDER` delivers the HTML email to the `USER EMAIL INBOX`

### Questions

- If time is limited and we can only test things one way for each service (API/E2E/Unit/UI/Manual) what kind of test should each service have?
- What tools would you use to implement those tests in the CI/CD pipeline as well as the developer workflows?
- In what stages (on commit/on merge/on staging deploy/on production deploy) of the delivery pipeline should the tests run?
- What actions should the system perform if any of the above mentioned tests fail?
- Can the services (or their requests/responses) be modified to make the flow easier to test?
- Developer `Klaus` makes a change in the `DATA SERVICE`, how can he ensure his change does not break the flow?

## Programming test

### downloadFile

- What is important to test here?
- Could this function be improved from a testability standpoint?

```typescript
async function downloadFile(
  fileUrl: string,
  filename: string,
  sendReceipt: boolean,
  user: User
): Promise<FileDownloadResult> {
  const downloadService = new MyDownloadService();
  const emailService = new MyEmailService();

  let path = filename;

  if (!filename.beginsWith('/downloads/')) {
    path = `/downloads/${filename}`;
  }

  const result = await downloadService.downloadRemoteFile(fileUrl, path);

  if (sendReceipt) {
    await emailService.emailDownloadReceipt(result.receipt, user.email);
  }

  return result;
}
```

### capitalizeName

- Is this function important to test?
- If so, what would the different test cases be (input/expected output)?

```typescript
function capitalizeName(name: string): string {
  if (name.length === 0) {
    return name;
  }
  if (name.length === 1) {
    return name[0].toUpperCase();
  }

  return name[0].toUpperCase() + name.substr(1);
}
```

