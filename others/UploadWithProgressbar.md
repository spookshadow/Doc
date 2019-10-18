>>> https://github.com/Hello-Mango/MAutoUpdate

>>> Progress bar with HttpClient https://stackoverflow.com/questions/20661652/progress-bar-with-httpclient


// Your original code.
HttpClientHandler aHandler = new HttpClientHandler();
aHandler.ClientCertificateOptions = ClientCertificateOption.Automatic;
HttpClient aClient = new HttpClient(aHandler);
aClient.DefaultRequestHeaders.ExpectContinue = false;
HttpResponseMessage response = await aClient.GetAsync(
    url,
    HttpCompletionOption.ResponseHeadersRead); // Important! ResponseHeadersRead.

// To save downloaded image to local storage
var imageFile = await ApplicationData.Current.LocalFolder.CreateFileAsync(
    filename,
    CreationCollisionOption.ReplaceExisting);
var fs = await imageFile.OpenAsync(FileAccessMode.ReadWrite);

// New code.
Stream stream = await response.Content.ReadAsStreamAsync();
IInputStream inputStream = stream.AsInputStream();
ulong totalBytesRead = 0;
while (true)
{
    // Read from the web.
    IBuffer buffer = new Windows.Storage.Streams.Buffer(1024);
    buffer = await inputStream.ReadAsync(
        buffer,
        buffer.Capacity,
        InputStreamOptions.None);

    if (buffer.Length == 0)
    {
        // There is nothing else to read.
        break;
    }

    // Report progress.
    totalBytesRead += buffer.Length;
    System.Diagnostics.Debug.WriteLine("Bytes read: {0}", totalBytesRead);

    // Write to file.
    await fs.WriteAsync(buffer);
}
inputStream.Dispose();
fs.Dispose();

>>> https://stackoverflow.com/questions/16416601/c-sharp-httpclient-4-5-multipart-form-data-upload

public static async Task<string> Upload(byte[] image)
{
     using (var client = new HttpClient())
     {
         using (var content =
             new MultipartFormDataContent("Upload----" + DateTime.Now.ToString(CultureInfo.InvariantCulture)))
         {
             content.Add(new StreamContent(new MemoryStream(image)), "bilddatei", "upload.jpg");

              using (
                 var message =
                     await client.PostAsync("http://www.directupload.net/index.php?mode=upload", content))
              {
                  var input = await message.Content.ReadAsStringAsync();

                  return !string.IsNullOrWhiteSpace(input) ? Regex.Match(input, @"http://\w*\.directupload\.net/images/\d*/\w*\.[a-z]{3}").Value : null;
              }
          }
     }
}


>>> 断点 https://blog.csdn.net/u012743824/article/details/79251295
