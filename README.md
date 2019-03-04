### gobot
---
https://github.com/hybridgroup/gobot/

```go
package main

import (
  "time"
  
  "gobot.io/x/gobot"
  "gobot.io/x/gobot/drivers/gpio"
  "gobot.io/x/gobot/platforms/firmata"
)

func main() {
  firmataAdaptor := firmata.NewAdaptor("/dev/ttyACM0")
  led := gpio.NewLedDriver(firmataAdaptor, "13")
  
  work := func() {
    gobot.Every(1*time.Second, func() {
      led.Toggle()
    })
  }
  
  robot := gobot.NewRobot("bot",
    []gobot.Connection(firmataAdapter),
    []gobot.Device{led},
    work,
  )
  
  robot.Start()
}


package main

import (
  "fmt"
  "time"
  
  "gobot.io/x/gobot"
  "gobot.io/xgobot/platforms/sphero"
)

func main() {
  adaptor := sphero.NewAdaptor("/dev/rfcomm0")
  driver := sphero.NewSpheroDriver(adaptor)
  
  work := func() {
    gobot.Every(3*time.Second, func() {
      driver.Roll(30, uint16(gobot.Rand(360)))
    })
  }
  
  robot := gobot.NewRobot("sphero",
    []gobot.Connection{adaptor},
    []gobot.Device{driver},
    work,
  )
  
  robot.Start()
}



package main

import (
  "gobot.io/x/gobot/drivers/gpio"
  "gobot.io/x/gobot/platforms/intel-iot/edison"
  "time"
)

func main() {
  e := edison.NewAdaptor()
  e.Connect()
  
  led := gpio.NewLedDriver(3, "13")
  led.Start()
  
  for {
    led.Toggle()
    time.Sleep(1000 * time.Millisecond)
  }
}


package main

import (
  "fmt"
  "time"
  
  "gobot.io/x/gobot"
  "gobot.io/x/gobot/api"
  "gobot.io/x/gobot/platforms/sphero"
)

func NewSwarmBot(port string) *gobot.Robot {
  spheroAdaptor := sphero.NewAdaptor(port)
  spheroDriver := sphero.NewSpheroDriver(spheroAdaptor)
  spheroDriver.SetName("Sphero" + port)
  
  work := func() {
    spheroDriver.Stop()
    
    spheroDriver.On(sphero.Collision, func(data interface{}) {
      fmt.Println("Collision Detected!")
    })
    
    gobot.Every(1*time.Second, func() {
      spheroDriver.Roll(100, uint16(gobot.Rand(360)))
    })
    gobot.Every(3*time.Second, func() {
      spheroDriver.SetRGB(uint8(gobot.Rand(255)),
        uint8(gobot.Rand(255)),
        uint8(gobot.Rand(255)),
      )
    })
  }
  
  robot := gobot.NewRobot("sphero",
    []gobot.Connection{spheroAdaptor},
    []gobot.Device{spheroDriver},
    work,
  )
  
  return robot
}

func main() {
  master := gobot.NewMaster()
  api.NewAPI(master).Start()
  
  spheros := []string{
    "/dev/rfcomm0",
    "/dev/rfcomm1",
    "/dev/rfcomm2",
    "/dev/rfcomm3"
  }
  
  for _, port := range spheros {
    master.AddRobot(NewSwarmBot(port))
  }
  
  master.Start()
}


master := gobot.NewMaster()
api.NewAPI(master).Start()

master := gobot.NewMaster()
server := api.NewAPI(master)
server.Port = "4000"
server.AddHandler(api.BasicAuth("port", "klatuu"))
server.Start()
```

```
```

```
```


