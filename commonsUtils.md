## commons

**DateFormatUtils**

~~~java
String nowDay = DateFormatUtils.format(nowDay,"yyyy-MM-dd HH:mm:ss");
DateFormatUtils.ISO_DATETIME_FORMAT.format(date);
~~~

**DateUtils**

~~~java
DateUtils.addYears(date,amount);
DateUtils.setYears(date,amount);
DateUtils.getFragmentInSeconds(date,Clendar.Mounth);//从这月开始过去多少天
isSameDay;toCalendar;
~~~

`DurationFormatUtils` 处理时间片段，formatDuration

`StopWatch` 计时器，start、stop、getTime方法

**FilenameUtils**

~~~java
FilenameUtils.getPrefix(filePath);
FilenameUtils.getFullPath(filePath);
FilenameUtils.concat(basePath,fullFilenameToAdd);
~~~

**FileUtils**

~~~java
FileUtils.copyFile(File src,File dest);
FileUtils.deleteDirectory(directory);
forceMkdir(File);moveDirectory(File src,File dest);
write(File file, CharSequence data, Charset encoding);
readFileToString(File file, Charset encoding);
~~~

**IOUtils**

~~~java
IOUtils.toString();
toCharArray/toByteArray
copy/read/write/close
~~~

**RegExUtils**

~~~java
removePattern
replacePattern
~~~

## other

**md5**

~~~java
// 拿到一个MD5转换器
MessageDigest md = MessageDigest.getInstance("MD5");
// 输入字符串转为字节数组
byte[] inputByteArray = input.getBytes();
// 将字节数组转换
md.update(inputByteArray);
// 返回结果
byte[] res = md.digest();
~~~

**Base64**

Base64 传输8Bit字节码的编码方式之一，基于64个可打印字符表示二进制数据。场景HTTP下传输二进制数据由于HTTP协议是文本协议，所以在HTTP协议下传输二进制数据需要将二进制数据转换字符数据；然而直接转换是不行的，因为网络传输只能传输可打印字符；

~~~Java
Base64.getEncoder().encodeToString(encoderStr.getBytes(charset));
new String(Base64.getUrlDecoder().decode(decoderStr),charset);
~~~

**java.nio.file.Files**

~~~java
Files.copy(Path from, Path to);
Files.move(Path from, Path to);
Files.delete(Path to);
Files.deleteIfExists(Path to);
Files.readAllLines(Path from, Charset.forName("utf-8"));
Files.write(from,content.getBytes(Charset.forName("utf-8")));
~~~



**jackson**

```java
// 转换为json字符串
objectMapper.writeValueAsString(o);
// 转换为JavaBean
objectMapper.readValue(jsonString, responseType);
```



**PinyinHelper**

~~~java
PinyinHelper.getShortPinyin(hanziStr);
// pinyinFormat 拼音格式 WITH_TONE_MARK（带声调）,WITHOUT_TONE（不带声调） WITH_TONE_NUMBER（带声调数字）
PinyinHelper.convertToPinyinString(pinYinStr, separator, pinyinFormat);
~~~

**NetworkInterface**

~~~java
// 获取本机所有的网络接口
// Enumeration 迭代集合 
Enumeration<NetworkInterface> nifs = NetworkInterface.getNetworkInterfaces();
// 获取网络接口IP
Enumeration<InetAddresses> address = nif.getInetAddresses();

~~~
