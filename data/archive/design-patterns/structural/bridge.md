# Structural Design patterns
## Bridge pattern

Bridge design pattern is used to decouple a class into two parts – abstraction and it’s implementation – so that both can evolve in future without affecting each other.

``
It increases the loose coupling between class abstraction and it’s implementation.
``
Usage examples: The Bridge pattern is especially useful when dealing with cross-platform apps, supporting multiple types of database servers or working with several API providers of a certain kind (for example, cloud platforms, social networks, etc.)

Here is an example. We have a ***device*** interface.
 
~
public interface Device {
     boolean isEnabled();
 
     void enable();
 
     void disable();
 
     int getVolume();
 
     void setVolume(int percent);
}
~

Imagine that ***TV*** is a Device!

~
public class TV implements Device {
    private boolean on = false;
    private int volume = 30;
    
    public boolean isEnabled() {
        return on;
    }
  
    public void enable() {
        on = true;
    }

    public void disable() {
        on = false;
    }

    public int getVolume() {
        return volume;
    }

    @Override
    public void setVolume(int volume) {
        if (volume > 100) {
            this.volume = 100;
        } else if (volume < 0) {
            this.volume = 0;
        } else {
            this.volume = volume;
        }
    }
}
~

And also ***Radio*** is a device!

~
public class Radio implements Device {
    private boolean on = false;
    private int volume = 30;

    @Override
    public boolean isEnabled() {
        return on;
    }

    @Override
    public void enable() {
        on = true;
    }

    @Override
    public void disable() {
        on = false;
    }

    @Override
    public int getVolume() {
        return volume;
    }

    @Override
    public void setVolume(int volume) {
        if (volume > 100) {
            this.volume = 100;
        } else if (volume < 0) {
            this.volume = 0;
        } else {
            this.volume = volume;
        }
    }
}
~

Any device needs a controller.
Here is a ***RemoteController*** class that uses ***Device*** to control it like below. 

~
public class RemoteController {
    protected Device device;

    public RemoteController() {}

    public RemoteController(Device device) {
        this.device = device;
    }

    public void power() {
        System.out.println("Remote: power toggle");
        if (device.isEnabled()) {
            device.disable();
        } else {
            device.enable();
        }
    }

    public void volumeDown() {
        System.out.println("Remote: volume down");
        device.setVolume(device.getVolume() - 10);
    }

    public void volumeUp() {
        System.out.println("Remote: volume up");
        device.setVolume(device.getVolume() + 10);
    }

}
~

Until now everything is pretty similar to Factory pattern. You can check <a href="/#/design-patterns-builder-factory" target="_blank">Factory Pattern</a> in my previous articles.

#### The differences start here:

Now imagine that we want to have different remotes the save way we did with device.
I define a ***Remote*** interface like below. 

~
public interface Remote {
    void power();

    void volumeDown();

    void volumeUp();
}
~

Let's have ***TvRemoteController*** class implements it via ***Remote*** interface for controlling ***TV*** device.
This helps us expand our Remote products easily as many types as we want!

~
public class TvRemoteController implements Remote {
    protected Device device;

    public TvRemoteController() {}

    public TvRemoteController(Device device) {
        this.device = device;
    }

    @Override
    public void power() {
        System.out.println("TV Remote: power toggle");
        if (device.isEnabled()) {
            device.disable();
        } else {
            device.enable();
        }
    }

    @Override
    public void volumeDown() {
        System.out.println("TV Remote: volume down");
        device.setVolume(device.getVolume() - 10);
    }

    @Override
    public void volumeUp() {
        System.out.println("TV Remote: volume up");
        device.setVolume(device.getVolume() + 10);
    }

}
~


We can now have ***RadioRemoteController*** like below for controlling **Radio** device.

~
public class RadioRemoteController implements Remote {
    
    protected Device device;

    public RadioRemoteController() {}

    public RadioRemoteController(Device device) {
        this.device = device;
    }

    @Override
    public void power() {
        System.out.println("Remote: power toggle");
        if (device.isEnabled()) {
            device.disable();
        } else {
            device.enable();
        }
    }

    @Override
    public void volumeDown() {
        System.out.println("Radio Remote: volume down");
        device.setVolume(device.getVolume() - 2);
    }

    @Override
    public void volumeUp() {
        System.out.println("Radio Remote: volume up");
        device.setVolume(device.getVolume() + 2);
    }

}
~ 

It simply has the same functionalities as ***RemoteController***
but has different volume scales to control device - also maybe Radio device is mechanical rather than digital ;)

Now you can see we have two vertical layers of hierarchy.
One side is ***Remote*** interface and other side is **Device*** interface at the same layer.
Other classes are below this two interfaces in the hierarchy.
So it looks like a bridge with this two interface connecting belonging classes to each other through interfaces!
 
We are just dealing with interfaces as bridge and don't care what is below them! 
Accessing other entities (from Remote to Device) is through bridge (here two interfaces)

Finally, we can use them like below. 

~
public class Demo {
    public static void main(String[] args) {
        testRadioDevice(new TV());
        testTvDevice(new Radio());
    }

    public static void testRadioDevice(Device device) {
        Remote remote = new RadioRemoteController(device);
        /* Power on the device */
        remote.power();
        /* Volume up and down the device */
        remote.volumeUp();
        remote.volumeUp();
        remote.volumeDown();
        remote.volumeDown();
    }
    
    public static void testTvDevice(Device device) {
        Remote remote = new TvRemoteController(device);
        /* Power on the device */
        remote.power();
        /* Volume up and down the device */
        remote.volumeUp();
        remote.volumeUp();
        remote.volumeDown();
        remote.volumeDown();
    }            
}
~ 

That was all about the bridge design pattern! As always thanks for reading this article.
You can find the whole code of this article at [this address](https://github.com/metao1/design-patterns/tree/master/src/main/java/com/metao/dp/bridge).
You can find the whole codes and another deign pattern examples in [link](https://github.com/metao1)

To support me simply give me a star in the [GitHub project of this project](https://github.com/metao1/design-patterns)


