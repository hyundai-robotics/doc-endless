# 3.3 Error Codes

| **Error** | **Message** | **Description** |
| :------: | :---------: | :------------- |
| E0108 | (axis 0) Encoder error: Encoder reset required | The encoder is out of usable range. Please correct the encoder offset and try again. |
| E0172 | (axis 0) Endless rotation position error | This error occurs during initialization when the difference between the backed-up encoder position and the absolute encoder value read at power-on is greater than 0x20000. If this error occurs, re-calibrate the encoder offset for the axis. |
| E0173 | Endless rotation overflow | A rotation amount exceeding the software's significant digits was specified. For large reduction ratio, even rotation counts below 1000 may be impossible to perform at once. Reduce the rotation count specified in the endless command. |
| E0193 | (axis 0) Encoder type not supported for endless | Only encoders with 1024, 2048, 4096, or 8192 pulses per motor revolution are supported by the endless feature. Other encoder types are not supported. |