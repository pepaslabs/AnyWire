# AnyWire

[Arduino](https://en.wikipedia.org/wiki/Arduino) abstraction layer
to support both [Wire](https://www.arduino.cc/reference/en/language/functions/communication/wire/)
and [SoftwareWire](https://www.arduino.cc/reference/en/libraries/softwarewire/).

Included in this repo are a local copy of [SoftwareWire](https://github.com/Testato/SoftwareWire)
and a forked copy of [HTU2xD_SHT2x_Si70xx](https://github.com/enjoyneering/HTU2xD_SHT2x_Si70xx),
which has been modified to support AnyWire.

See the included `demo.ino`, which reads from both hardware and software I2C sensors
(["GY-21"](https://www.ebay.com/sch/i.html?_nkw=gy-21) breakout boards).

```cpp
// an SHT21 sensor using hardware I2C:
HTU2xD_SHT2x_SI70xx hw_sht21(HTU2xD_SENSOR, HUMD_12BIT_TEMP_14BIT);

// an SHT21 sensor using software I2C:
uint8_t sw_sda_pin = 12;
uint8_t sw_scl_pin = 11;
SoftwareWire swire(sw_sda_pin, sw_scl_pin);
HTU2xD_SHT2x_SI70xx sw_sht21(HTU2xD_SENSOR, HUMD_12BIT_TEMP_14BIT, &swire);
```

![Screen Shot 2022-08-04 at 12 51 50 AM](https://user-images.githubusercontent.com/223396/182774095-247ccadb-fcd0-4be2-8878-ef9eb56ea2ec.png)

![IMG_1532](https://user-images.githubusercontent.com/223396/182774283-f844ed3e-02dd-4ecd-a89c-0bdf09546764.jpg)


## Caveats

Not all of the [Wire API](https://www.arduino.cc/reference/en/language/functions/communication/wire/) is supported by SoftwareWire.
For the unsupported portions, AnyWire simply enters into an infinite loop:

```cpp
bool AnyWire::getWireTimeoutFlag(void) {
  if (_softwareWire) {
    while (1) { __asm volatile(""); } // not supported
  } else {
    return Wire.getWireTimeoutFlag();
  }
}
```

These include:
- `begin()` when called with an address
- `setWireTimeout()` when called with `reset_with_timeout = true`
- `getWireTimeoutFlag()`
- `clearWireTimeoutFlag()`
- `requestFrom()` when called with a second "internal" address and size
- `flush()`


## License

`AnyWire.h`, `AnyWire.cpp` and `demo.ino` are copyright 2022 Jason Pepas,
released under the terms of the MIT License.  See https://opensource.org/licenses/MIT.

`SoftwareWire.h` and `SoftwareWire.cpp` are copied from https://github.com/Testato/SoftwareWire,
which is GPL-3.0 licensed.

`HTU2xD_SHT2x_Si70xx.h` and `HTU2xD_SHT2x_Si70xx.cpp` are forked from https://github.com/enjoyneering/HTU2xD_SHT2x_Si70xx,
which is GPL-3.0 licensed.
