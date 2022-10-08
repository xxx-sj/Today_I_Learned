데코레이터 패턴 in java [java I/O]

``` 
데코레이터 패턴을 적용한 java io를 통해 확인해보려한다.
```

```java
[component]
public abstract class InputStream implements Closeable {
}


[concrete component]
public class FileInputStream extends InputStream {}

public class ByteArrayInputStream extends InputStream {}
...



[concrete decorator]
// 단순히 inputstream을 구현하였다.
// FilterInputStream을 구현한 클래스들이 concrete decorator 역할을 하게된다.

FilterInputStream에는 데이터의 기본 소스로 사용하는 다른 입력 스트림이 포함되어 있으며,   
이를 통해 데이터를 변환하거나 추가 기능을 제공할 수 있습니다.
FilterInputStream 클래스 자체는 포함된 입력 스트림에 모든 요청을 전달하는 버전으로    
InputStream의 모든 메서드를 단순히 재정의합니다. FilterInputStream의 하위 클래스는 이러한 메서드 중    
일부를 추가로 재정의할 수 있으며 추가 메서드 및 필드를 제공할 수도 있습니다.
- 번역에서

public class FilterInputStream extends InputStream {
    protected volatile InputStream in;
    
    protected FilterInputStream(InputStream in) {
        this.in = in;
    }
}


[concrete decorator]
public class BufferedInputStream extends FilterInputStream {

    public BufferedInputStream(InputStream in, int size) {
        super(in);
        if (size <= 0) {
            throw new IllegalArgumentException("Buffer size <= 0");
        }
        buf = new byte[size];
    }
}


public class InflaterInputStream extends FilterInputStream {
    public InflaterInputStream(InputStream in, Inflater inf, int size) {
        super(in);
        if (in == null || inf == null) {
            throw new NullPointerException();
        } else if (size <= 0) {
            throw new IllegalArgumentException("buffer size <= 0");
        }
        this.inf = inf;
        buf = new byte[size];
    }
}


public class ZipInputStream extends InflaterInputStream implements ZipConstants {
    public ZipInputStream(InputStream in, Charset charset) {
        super(new PushbackInputStream(in, 512), new Inflater(true), 512);
        usesDefaultInflater = true;
        if (in == null) {
            throw new NullPointerException("in is null");
        }
        if (charset == null)
            throw new NullPointerException("charset is null");
        this.zc = ZipCoder.get(charset);
    }


}


class Main {
  public static void main(String[] args) {
    
     try {
            InputStream in =
                    new ZipInputStream(
                        new BufferedInputStream(
                            new FileInputStream("text.txt")));

        } catch(Exception e) {
            //do something
        }  
  }
}


// in ZipInputStream

public int read(byte[] b, int off, int len) throws IOException {
        ensureOpen();
        if (off < 0 || len < 0 || off > b.length - len) {
            throw new IndexOutOfBoundsException();
        } else if (len == 0) {
            return 0;
        }

        if (entry == null) {
            return -1;
        }
        switch (entry.method) {
        case DEFLATED:
            len = super.read(b, off, len);
            if (len == -1) {
                readEnd(entry);
                entryEOF = true;
                entry = null;
            } else {
                crc.update(b, off, len);
            }
            return len;
        case STORED:
            if (remaining <= 0) {
                entryEOF = true;
                entry = null;
                return -1;
            }
            if (len > remaining) {
                len = (int)remaining;
            }
            **** len = in.read(b, off, len); **** 
            if (len == -1) {
                throw new ZipException("unexpected EOF");
            }
            crc.update(b, off, len);
            remaining -= len;
            if (remaining == 0 && entry.crc != crc.getValue()) {
                throw new ZipException(
                    "invalid entry CRC (expected 0x" + Long.toHexString(entry.crc) +
                    " but got 0x" + Long.toHexString(crc.getValue()) + ")");
            }
            return len;
        default:
            throw new ZipException("invalid compression method");
        }
    }


```
