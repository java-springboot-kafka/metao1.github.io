# Creational design patterns
## Builder pattern

Separate the construction of a complex object from its representation so that the same construction process can create different representations.

``
Allows you to create different flavors of an object while avoiding constructor pollution. Useful when there could be several flavors of an object. Or when there are a lot of steps involved in creation of an object.
``

Usage examples: Imagine a character generator for a role playing game. The easiest option is to let computer create the character for you. But if you want to select the character details like profession, gender, hair color etc. the character generation becomes a step-by-step process that completes when all the selections are ready.

Wikipedia says

``
The builder pattern is an object creation software design pattern with the intentions of finding a solution to the telescoping constructor anti-pattern.
``

Having said that let me add a bit about what telescoping constructor anti-pattern is. At one point or the other we have all seen a constructor like below:

~
public Hero(Profession profession, String name, HairType hairType, HairColor hairColor, Armor armor, Weapon weapon) {
}
~

###Programmatic Example

The sane alternative is to use the Builder pattern. First of all we have our hero that we want to create

~
public final class Hero {
  private final Profession profession;
  private final String name;
  private final HairType hairType;
  private final HairColor hairColor;
  private final Armor armor;
  private final Weapon weapon;

  private Hero(Builder builder) {
    this.profession = builder.profession;
    this.name = builder.name;
    this.hairColor = builder.hairColor;
    this.hairType = builder.hairType;
    this.weapon = builder.weapon;
    this.armor = builder.armor;
  }
}
~

And then we have the builder

~
public static class Builder {
    private final Profession profession;
    private final String name;
    private HairType hairType;
    private HairColor hairColor;
    private Armor armor;
    private Weapon weapon;

    public Builder(Profession profession, String name) {
      if (profession == null || name == null) {
        throw new IllegalArgumentException("profession and name can not be null");
      }
      this.profession = profession;
      this.name = name;
    }

    public Builder withHairType(HairType hairType) {
      this.hairType = hairType;
      return this;
    }

    public Builder withHairColor(HairColor hairColor) {
      this.hairColor = hairColor;
      return this;
    }

    public Builder withArmor(Armor armor) {
      this.armor = armor;
      return this;
    }

    public Builder withWeapon(Weapon weapon) {
      this.weapon = weapon;
      return this;
    }

    public Hero build() {
      return new Hero(this);
    }
  }
~

And then it can be used as:

~
var mage = new Hero.Builder(Profession.MAGE, "Riobard").withHairColor(HairColor.BLACK).withWeapon(Weapon.DAGGER).build();
~

#### Real world examples
```
java.lang.StringBuilder
java.nio.ByteBuffer as well as similar buffers such as FloatBuffer, IntBuffer and so on.
java.lang.StringBuffer
All implementations of java.lang.Appendable
Apache Camel builders
Apache Commons Option.Builder
```

That was all about the builder design pattern! As always thanks for reading this article.
You can find the whole code of this article at [this address](https://github.com/metao1/design-patterns/tree/master/src/main/java/com/metao/dp/builder).
Also another deign pattern examples are in [this repository](https://github.com/metao1)

To support me simply give me a star [GitHub project of this project](https://github.com/metao1/design-patterns)


