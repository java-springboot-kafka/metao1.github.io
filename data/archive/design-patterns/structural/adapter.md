# Structural Design patterns
## Adapter pattern

Ever tried to use your camera memory card in your laptop. You cannot use it directly simply because there is no port in the laptop which accept it.You must use a compatible card reader.
You put your memory card into the card reader and then inject the card reader into the laptop. This card reader can be called the adapter.

A similar example is your mobile charger, or your laptop charger which can be used with any power supply without fear of the variance power supply in different locations. That is also called power “adapter”. 

In programming as well, adapter pattern used for similar purposes. It enables two incompatible interfaces to work smoothly with each other. Going by definition:


```Adapter design pattern is one of the structural design pattern and its used so that two unrelated interfaces can work together. The object, that joins these unrelated interfaces, is called an Adapter.```

Adapter pattern is helpful when some other existing components you want to adapt  by the existing code but you have not access to the source code.

Take a look at the typical interaction like this:

<ul class="banner-landing-nav" markdown="1">
    <li class="image-part-avatar" markdown="1">
        ![adapter_design_pattern](data/cv/adapter_sequence.png)    
    </li>
</ul>


Here the **Adaptee** functions are not accessible for Client so client uses an **Adapter** to call the required functions.

Let's start with one example. 

Consider a **MusicPlayer** needs to play musics with different formats. It does not have access to **MusicConverter**
To give **MusicPlayer** access to any converter we use **MusicAdapter** that can negotiate in between.
Simply put any kind of Music formats e.g **MP3Format** or "MP4Format" and adapter facilitates stream from MusicPlayer
to Converter, and the converter returns the expected stream as output stream.
Pretty good idea! 
Now let's take a look at each entity separately below.

**MP3Format** as a domain class contains all the relevant methods and attributes. 
Here **convertToMP3** does convert to mp3 format.
  
~
public class MP3Format implements MusicConverter {
    OutputStream convertToMP3(String musicType, InputStream inputStream){
        //do convert 
    }
}
~

**MusicConverter** is an interface. It just needs to get musicType and inputStream as arguments 
and returns the OutputStream; OutputStream can be used for **MusicPlayer** to play a song with!

~
public interface MusicConverter {
    
    /* This is the method for using in adapter */
    OutputStream convertTo(String musicType, InputStream inputStream);
} 
~
 
Here **MusicAdapter** just should have the same methods that **MP3Format** class has.
So we implement **MusicConverter** and **convertTo** is available inside the class.
 
~ 

public class MusicAdapter implements MusicConverter {
  
    @Override
    OutputStream convertTo(String musicType, InputStream inputStream) {
     
        /* one can use switch-case here to determine the propper call */
        // converts to any music type based-on the musicType
        final Mp3Format mp3Format = new MP3Format();
        return mp3Format.convertToMP3(inputStream);     
    }
}       
~

**MusicPlayer** class simply creates **MusicAdapter** object to call the converter method.
You can pass **OutputStream** that actually is comming from a converter and is transparent for
**MusicPlayer** class.
   
~
public class MusicPlayer {
    
    void playMusic(){
        // read file and put in one inputStream
        // InputStream inputStream = ...
        MusicAdapter musicAdapter = new MusicAdapter();
        OutputStream outputStream = musicAdapter.convertTo("mp3", inputStream) {
        //...pass outputStream to player       
    }    
}
~

This was a simple case of using adapter pattern to access. It is using one class 
as mediator to access other classes. 

Let me go further and make our adapter a little better.

~ 

public class MusicAdapter implements MusicConverter {
  
    @Override
    OutputStream convertTo(String musicType, InputStream inputStream) {
     
        /* one can use switch-case here to determine the propper call */
        // converts to any music type based-on the musicType
        final Mp3Format mp3Format = new MP3Format();
        return mp3Format.convertToMP3(inputStream);     
    }
}       
~  

There is a better adapter to access any classes and converts every type like a swiss knife!

We said we can use a switch case inside the **convertTo** method to indentify the music format type.
For example if the format is mp3 we simply check the string and create one object of **MP3Format**.

If you want to support mp4 formats for example , then you simply can add new class called MP4Format 
and so on. As you can see inside the **convertTo** method you don't see all the supported formats.
Specially this is the case when you deliver this class in a library for other developers in the team
,and they need to know which format supported. It is unlikely they check the implementation of the method.
All of this problem comes because string variable "musicType" can accept any character and 
one may have typos then he will get an nullException as returned value.
To minimize the error I like the idea to pass an interface instead of a string type as an argument.

#### how this helps?

Let me rewrite the  **MusicConverter** interface revisely.
    

We have a pretty nice inheritance of **MusicFormat** as we can define here like this.

~
public interface MusicFormat {

     /* This is the method for using in adapter */
     OutputStream convertTo(InputStream inputStream);
}
 
~

**MP3Format** is a music format so is the **MP4Format** and so on. 

~
public class MP3Format implements MusicFormat {

    @Override
    OutputStream convert(InputStream inputStream){
        //Convert to mp3 format 
    }
}
~

And now we can support mp4 format too!

~
public class MP4Format implements MusicFormat {

    @Override
    OutputStream convert(InputStream inputStream){
        //Convert to mp4 format 
    }
}
~

Let's go further and make our adapter a little better.

~ 

public class MusicAdapter {
  
   
    OutputStream convertTo(MusicFormat musicFormat, InputStream inputStream) {
     
        // converts to any music type based-on the musicType       
        return musicFormat.convert(inputStream);     
    }
}       
~  

Our adapter is very simple now! That is all a music adapter now should have.
We could reduce complexity and increase the maintainability of our code.

At the end our music player would look like below:

~
public class MusicPlayer {
    
    void playMusic(){
        // read file and put in one inputStream
        // InputStream inputStream = ...
        final MusicAdapter musicAdapter = new MusicAdapter();
        try(OutputStream mp3OutStream = musicAdapter.convertTo(new MP3Format(), inputStream)){
            //...pass outputStream to player
            //...save it to a file    
        }      
               
        try(OutputStream mp4OutStream = musicAdapter.convertTo(new MP4Format(), inputStream)){      
            //...pass outputStream to player
            //...save it to a file
        }
    }    
}
~

That was all about the adapter design pattern! As always thanks for reading this article.
You can find the whole code of this article at [this address](https://github.com/metao1/design-patterns/tree/master/src/main/java/com/metao/dp/adapter).
You can find the whole codes and another deign pattern examples in [link](https://github.com/metao1)

To support me simply give me a star in the [GitHub project of this project](https://github.com/metao1/design-patterns)

