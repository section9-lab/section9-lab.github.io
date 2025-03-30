---
title: JsonMsgPack
date: 2022-01-12
category:
  - Tools
tag:
  - java
  - util
star: false
sticky: false
---

# JsonMsgPack



maven
```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.6</version>
</dependency>

<dependency>
    <groupId>org.msgpack</groupId>
    <artifactId>jackson-dataformat-msgpack</artifactId>
    <version>0.9.0</version>
</dependency>
```

JacksonUtil

> .setSerializationInclusion(JsonInclude.Include.NON_NULL);空值不参与序列化。

```java
public class JsonKit {
    private static final ObjectMapper MsagePakMapper = new ObjectMapper(new MessagePackFactory());
    private static final ObjectMapper JsonMapper = new ObjectMapper(new JsonFactory());

    public static <T> String jsonBeanToStr(Object obj) {
        try {
            return JsonMapper.writeValueAsString(obj);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static <T> String msgpackBeanToStr(Object obj) {
        try {
            return MsagePakMapper.writeValueAsString(obj);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static <T> byte[] jsonBeanToByteArr(Object obj) {
        try {
            return JsonMapper.writeValueAsBytes(obj);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static <T> byte[] msgpackBeanToByteArr(Object obj) {
        try {
            return MsagePakMapper.writeValueAsBytes(obj);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static <T> T jsonStrToBean(String jsonStr, Class<T> valueType) {
        try {
            JsonMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
            return JsonMapper.readerFor(valueType).readValue(jsonStr);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static <T> T msgpackStrToBean(String jsonStr, Class<T> valueType) {
        try {
            return MsagePakMapper.readerFor(valueType).readValue(jsonStr);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static <T> T jsonFileToBean(String path, Class<T> valueType) {
        try {
            JsonMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
            return JsonMapper.readValue(new File(path), valueType);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static <T> T msgpackFileToBean(String path, Class<T> valueType) {
        try {
            MsagePakMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
            return MsagePakMapper.readValue(new File(path), valueType);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static <T> T jsonStrToBean(String jsonStr) {
        try {
            return JsonMapper.readerFor(Map.class).readValue(jsonStr);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static <T> T msgpackStrToBean(String jsonStr) {
        try {
            return MsagePakMapper.readerFor(Map.class).readValue(jsonStr);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```
