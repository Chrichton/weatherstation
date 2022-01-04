# SensorHub

**TODO: Add description**

## Targets

Nerves applications produce images for hardware targets based on the
`MIX_TARGET` environment variable. If `MIX_TARGET` is unset, `mix` builds an
image that runs on the host (e.g., your laptop). This is useful for executing
logic tests, running utilities, and debugging. Other targets are represented by
a short name like `rpi3` that maps to a Nerves system image for that platform.
All of this logic is in the generated `mix.exs` and may be customized. For more
information about targets see:

https://hexdocs.pm/nerves/targets.html#content

## Getting Started

To start your Nerves app:
  * `export MIX_TARGET=my_target` or prefix every command with
    `MIX_TARGET=my_target`. For example, `MIX_TARGET=rpi3`
  * Install dependencies with `mix deps.get`
  * Create firmware with `mix firmware`
  * Burn to an SD card with `mix firmware.burn`

## Learn more

  * Official docs: https://hexdocs.pm/nerves/getting-started.html
  * Official website: https://nerves-project.org/
  * Forum: https://elixirforum.com/c/nerves-forum
  * Discussion Slack elixir-lang #nerves ([Invite](https://elixir-slackin.herokuapp.com/))
  * Source: https://github.com/nerves-project/nerves

## Upload new firmware

### VS-Code-Terminal
* export MIX_TARGET=rpi0

* new zsh
* cd .ssh
* mv id_rsa.pub-yubikey id_rsa.pub
* mix firmware
* mix upload
* mv id_rsa.pub id_rsa.pub-yubikey

### VS-Code-Terminal
* ssh nerves.local

## Detect Devices

* Circuits.I2C.detect_devices

## SGP30 Gas Sensor (Bosch)

https://hex.pm/packages/sgp30

* {:ok, sgp} = SGP30.start_link()
* SGP30.state #(every second a new measurement is started)

%SGP30{
  address: 88,
  co2_eq_ppm: 400,
  ethanol_raw: 17258,
  h2_raw: 12604,
  i2c: #Reference<0.3582714447.268828675.192322>,
  serial: 28876075,
  tvoc_ppb: 0
}

## BME680 Environmental Sensor (Bosch)

###
https://hex.pm/packages/BMP280

* BMP280.start_link([i2c_address: 0x77, name: BMP280])
* BMP280.read(BMP280)

{:ok,
 %BMP280.Measurement{
   altitude_m: -21.582447576278796,
   dew_point_c: 11.672216761572129,
   gas_resistance_ohms: 5605.027499703102,
   humidity_rh: 47.502034595712374,
   pressure_pa: 100256.10940496085,
   temperature_c: 23.468890934417686,
   timestamp_ms: 123672
 }}

###
https://hex.pm/packages/elixir_bme680

* {:ok, pid} = Bme680.start_link(i2c_address: 0x77)
* measurement = Bme680.measure(pid)

%Bme680.Measurement{
  gas_resistance: 2065,
  humidity: 49.47,
  pressure: 1002.54,
  temperature: 22.66
}

###
Note that, due to the nature of the BME680 gas resistance sensor, the gas resistance measurement needs a warm-up in order to give stable measurements. One possible strategy is to perform continuous meaurements in a loop until the value stabilizes. That might take from a few seconds to several minutes (or more when the sensor is brand new).

## AS7262 Spectal Sensor

https://www.sparkfun.com/products/14347

https://github.com/sparkfun/Sparkfun_AS726X_Arduino_Library

## Starting sensors with Supervisor

* Supervisor.which_children(SensorHub.Supervisor)
[
  {BMP280, #PID<0.1207.0>, :worker, [BMP280]},
  {SGP30, #PID<0.1206.0>, :worker, [SGP30]}
]

* BMP280 |> Sensor.new() |> Sensor.measure()
%{
  altitude_m: 39.76537983156137,
  pressure_pa: 99529.50900235256,
  temperature_c: 23.325976088028256
}
