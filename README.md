### Running Embassy's Blinky code on STs NUCLEO-64 STM32L010 evaluation board.

Notes:
- Embassy repository: https://github.com/embassy-rs/embassy
- The microcontroller on the evaluation board is an STM32L010RBT6
- Schematic: https://www.st.com/en/evaluation-tools/nucleo-l010rb.html#cad-resources

First, clone the Embassy repository:

```git clone https://github.com/embassy-rs/embassy```

cd into embassy and type ```code .``` to open VSCode with the current folder as the root folder.

In VSCode go into the (hidden) .cargo folder ```examples/stm32l0/.cargo``` and edit ```config.toml``` to update the chip type:

```
[target.'cfg(all(target_arch = "arm", target_os = "none"))']
# replace your chip as listed in `probe-rs chip list`
runner = "probe-rs run --chip STM32L010RBTx"

[build]
target = "thumbv6m-none-eabi"

[env]
DEFMT_LOG = "trace"
```

In VSCode find cargo.toml in the root folder and update the microcontroller type  to ```stm32l010rb``` under:

 ```
 [dependencies]
 embassy-stm32 = ...
 ```

In VSCode open src/blinky.rs and modify the LED ports as follows:

```
#![no_std]
#![no_main]

use defmt::*;
use embassy_executor::Spawner;
use embassy_stm32::gpio::{Level, Output, Speed};
use embassy_time::Timer;
use {defmt_rtt as _, panic_probe as _};
use embassy_stm32::peripherals::

#[embassy_executor::main]
async fn main(_spawner: Spawner) {
    let p = embassy_stm32::init(Default::default());
    info!("Hello World!");

                                                                // Change LED port as follows
    let mut led = Output::new(p.PA5, Level::High, Speed::Low);  // LED on port PA5

    loop {
        info!("high");
        led.toggle();
        //led.set_high();
        Timer::after_millis(500).await;

        info!("low");
        led.toggle();
        //led.set_low();
        Timer::after_millis(500).await;
    }
}
```

Connect the NUCLEO board USB to the computer. From the terminal cd into ```examples/stm32l0``` then compile and run blinky using:

```cargo run --bin blinky```

This should eventually result in:

```
Compiling embassy-template-stm32l0 v0.1.0 (/home/Github/embassy/examples/stm32l0)
  Finished dev [unoptimized + debuginfo] target(s) in 0.29s
    Running `probe-rs run --chip STM32L010RBTx target/thumbv6m-none-eabi/debug/blinky`
      Erasing ✔ [00:00:08] [##################################] 59.00 KiB/59.00 KiB @ 6.63 KiB/s (eta 0s )
      Programming ✔ [00:00:05] [#################################] 59.00 KiB/59.00 KiB @ 10.67 KiB/s (eta 0s )    Finished in 14.44s
```

The Blinky program code has compiled and uploaded into the microcontroller on the eval board. The green LED on the eval board should be blinking on and off.

To change the LED flashing rate, edit the delay times in ```main.rs``` then save and rerun.
