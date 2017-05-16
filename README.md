## Android串口通信

- [Googel官方文档](https://code.google.com/archive/p/android-serialport-api/)
- [Android串口通信Demo](https://github.com/cepr/android-serialport-api)

```java
public class SerialPort {

    static {
        System.loadLibrary("serial_port");
    }

    private FileDescriptor   mFileDescriptor;
    private FileInputStream  mFileInputStream;
    private FileOutputStream mFileOutputStream;

    public SerialPort(File device, int baudRate, int flags) throws SecurityException, IOException {
        // 检查访问权限
        if (!device.canRead() || !device.canWrite()) {
            try {
                Process su;
                su = Runtime.getRuntime().exec("/system/bin/su");
                String cmd = "chmod 666 " + device.getAbsolutePath() + "\n" + "exit\n";
                su.getOutputStream().write(cmd.getBytes());
                if ((su.waitFor() != 0) || !device.canRead() || !device.canWrite()) {
                    throw new SecurityException();
                }
            } catch (Exception e) {
                e.printStackTrace();
                throw new SecurityException();
            }
        }

        mFileDescriptor = open(device.getAbsolutePath(), baudRate, flags);
        if (mFileDescriptor == null) {
            throw new IOException();
        }
        mFileInputStream = new FileInputStream(mFileDescriptor);
        mFileOutputStream = new FileOutputStream(mFileDescriptor);
    }

    public void closeSerialPort() {
        close();
    }

    public FileInputStream getInputStream() {
        return mFileInputStream;
    }

    public OutputStream getOutputStream() {
        return mFileOutputStream;
    }

    private native static FileDescriptor open(String path, int baudRate, int flags);

    private native void close();
}
```
