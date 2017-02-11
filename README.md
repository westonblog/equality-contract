Why the equality contract is a mistake
==

In both Java and C# (+other lanaguages, but I'm going to focus on those), all classes have a method `boolean equals(Object other)`

This method is to allow comparison with another object and tell you whether they are "equal" or "not equal". The default method in `Object` tells you whether the instances are the same instance of the class.

A common use case for the equality contract would be in a `HashSet`, or `HashMap`.

```
Set<Person> people = new HashSet<>();
people.add(new Person("John Doe"));
people.add(new Person("John Doe"));
int size = set.size(); //what is this?
```

Now I added two `Person`s that look equal, but if I don't implement the equality contract, I'll see a `size` of `2` because it's defaulting to the `Object`s `hashcode` and `equal`s method.

So what does the equality implementation look like for `Person`, something like this:

```
public class Person {
    private final String name;

    public Person(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Person person = (Person) o;

        return name != null ? name.equals(person.name) : person.name == null;
    }

    @Override
    public int hashCode() {
        return name != null ? name.hashCode() : 0;
    }
}

```

So the issues I have had so far is: I was able to create a generic `HashSet` with a type that it knew should implement equality contract, but that because `Object` implements it, it cannot be added as a generic constraint. So I received no warnings about my lack of equality implementation.

Second, I have to type check `getClass() != o.getClass()` and type cast `Person person = (Person) o;` inside every `equals` I write.

So we have a generic set of type `Person` that then down casts it to an `Object` for `equals` call, which then checks the type and casts it back. This is a crazy situation.

In both Java and C# the root cause is history, `equal`s pre-dates generics in both cases, but I hope to show that there were other options, and there are still other options for this language and future languages.

