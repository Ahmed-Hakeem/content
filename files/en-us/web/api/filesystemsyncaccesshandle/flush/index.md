---
title: "FileSystemSyncAccessHandle: flush() method"
short-title: flush()
slug: Web/API/FileSystemSyncAccessHandle/flush
page-type: web-api-instance-method
browser-compat: api.FileSystemSyncAccessHandle.flush
---

{{securecontext_header}}{{APIRef("File System API")}}

The **`flush()`** method of the
{{domxref("FileSystemSyncAccessHandle")}} interface persists any changes made to the file associated with the handle via the {{domxref('FileSystemSyncAccessHandle.write', 'write()')}} method to disk.

Bear in mind that you only need to call this method if you need the changes committed to disk at a specific time, otherwise you can leave the underlying operating system to handle this when it sees fit, which should be OK in most cases.

> **Note:** In earlier versions of the spec, {{domxref("FileSystemSyncAccessHandle.close()", "close()")}}, `flush()`, {{domxref("FileSystemSyncAccessHandle.getSize()", "getSize()")}}, and {{domxref("FileSystemSyncAccessHandle.truncate()", "truncate()")}} were wrongly specified as asynchronous methods, and older versions of some browsers implement them in this way. However, all current browsers that support these methods implement them as synchronous methods.

## Syntax

```js-nolint
flush()
```

### Parameters

None.

### Return value

A {{jsxref('Promise')}} which resolves to undefined.

### Exceptions

None.

## Examples

The following asynchronous event handler function is contained inside a Web Worker. On receiving a message from the main thread it:

- Creates a synchronous file access handle.
- Gets the size of the file and creates an {{jsxref("ArrayBuffer")}} to contain it.
- Reads the file contents into the buffer.
- Encodes the message and writes it to the end of the file.
- Persists the changes to disk and closes the access handle.

```js
onmessage = async (e) => {
  // Retrieve message sent to work from main script
  const message = e.data;

  // Get handle to draft file
  const root = await navigator.storage.getDirectory();
  const draftHandle = await root.getFileHandle("draft.txt", { create: true });
  // Get sync access handle
  const accessHandle = await draftHandle.createSyncAccessHandle();

  // Get size of the file.
  const fileSize = accessHandle.getSize();
  // Read file content to a buffer.
  const buffer = new DataView(new ArrayBuffer(fileSize));
  const readBuffer = accessHandle.read(buffer, { at: 0 });

  // Write the message to the end of the file.
  const encoder = new TextEncoder();
  const encodedMessage = encoder.encode(message);
  const writeBuffer = accessHandle.write(encodedMessage, { at: readBuffer });

  // Persist changes to disk.
  accessHandle.flush();

  // Always close FileSystemSyncAccessHandle if done.
  accessHandle.close();
};
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [File System API](/en-US/docs/Web/API/File_System_API)
- [The File System Access API: simplifying access to local files](https://web.dev/file-system-access/)
