---
title: A couple of katas for the Java Collectors API
subtitle: Let's see how jekyll handles rendering code
tags:
- Programming
- Java
cover-img: assets/img/post/java_collectors_1.png
date: '2022-06-06 20:30:00'
---

I was once in a team that really believed in functional java. Much of my passion on the subject today stems from that time.  

As that team grew, we had to onboard a number of junior and senior hires. Some of them were getting restless sitting in a corner reading `Java 8 in action` during their onboarding, so they were asking for something practical to do.  

This led to me developing a series of excersices on the streaming and collectors APIs. By the end I had around 10 of these that I used to give to new joiners, each testing  a specific concept. They were all purely optional, but I had gotten good feedback from the people that chose to do them.  

Those were all closed source, but I've recreated a couple of them in the same spirit as I remember them bellow. Let's see how this blog theme deals with rendering code! 

### Address.java

Given a simple example data class with some fields: 

```java
package net.samatas.example.streams.intro;

import java.util.Objects;

public class Address {
    private final String region;
    private final String city;
    private final String road;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Address address = (Address) o;
        return region.equals(address.region) &&
                city.equals(address.city) &&
                road.equals(address.road);
    }

    @Override
    public int hashCode() {
        return Objects.hash(region, city, road);
    }

    public String getRoad() {
        return road;
    }

    public String getCity() {
        return city;
    }

    public String getRegion() {
        return region;
    }

    public Address(String region, String city, String road) {
        this.region = region;
        this.city = city;
        this.road = road;
    }
}
```

Write a solution that uses only the java Collectors API for the following: 

```java
package net.samatas.example.streams.intro;

import org.junit.jupiter.api.Test;

import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class CollectorsIntroTest {

    @Test
    public void regionByCity() {
        final Map<String, String> regionByCityMap = null; //INPUT_ADDRESS_LIST.stream().collect(/**/);
        assertEquals(TARGET_REGION_BY_CITY_MAP, regionByCityMap);
    }

    @Test
    public void roadsByCityAndRegion() {
        final Map<String, Map<String, List<String>>> roadsByCityAndRegionMap = null; //INPUT_ADDRESS_LIST.stream().collect(/**/);
        assertEquals(TARGET_ROADS_BY_CITY_AND_REGION_MAP, roadsByCityAndRegionMap);
    }

    private final List<Address> INPUT_ADDRESS_LIST = List.of(
            new Address("Cyprus", "Limassol", "Makariou"),
            new Address("Cyprus", "Nicosia", "Makariou"),
            new Address("Cyprus", "Limassol", "Rigenis"),
            new Address("Cyprus", "Paphos", "Armenion"),
            new Address("Greece", "Athens", "Syntagmatos"));

    private final Map<String, String> TARGET_REGION_BY_CITY_MAP = Map.of(
		"Limassol", "Cyprus", 
		"Nicosia", "Cyprus", 
		"Paphos", "Cyprus", 
		"Athens", "Greece");
		
    private final Map<String, Map<String, List<String>>> TARGET_ROADS_BY_CITY_AND_REGION_MAP = Map.of(
		"Cyprus", Map.of("Limassol", List.of("Makariou", "Rigenis"), "Nicosia", List.of("Makariou"), "Paphos", List.of("Armenion")),
		"Greece", Map.of("Athens", List.of("Syntagmatos")));
}
```
&nbsp;
**Now is the point in the post where I tell you to pause and give it a try, but nobody ever does that so let's just dive in!**
&nbsp;
&nbsp;
## 1.

```java
    @Test
    public void regionByCity() {
        final Map<String, String> regionByCityMap = null; //INPUT_ADDRESS_LIST.stream().collect(/**/);
        assertEquals(TARGET_REGION_BY_CITY_MAP, regionByCityMap);
    }
```
  
We all need to burn our hand at the stove from time to time to learn caution, so this test is designed to help you internalize that the default mergeFunction used by Collectors.toMap() throws an IllegalStateException if duplicate keys are inserted.   


The point is to get you to write the naive:  

```java
    @Test
    public void regionByCity() {
        final Map<String, String> regionByCityMap = INPUT_ADDRESS_LIST.stream().collect(Collectors.toMap(Address::getCity, Address::getRegion));
        assertEquals(TARGET_REGION_BY_CITY_MAP, regionByCityMap);
    }
```  

and the very helpful exception:  

{: .box-error} 
Duplicate key Limassol (attempted merging values Cyprus and Cyprus)   


This will hopefully set you well on your way to understand what a merge function does. (Or if you already knew, more power to you!)  

Duplicate key exceptions due to this happened *all the time* in my old team, so I had a special place in my heart for anyone who got this point and guarded against it. 

A sample merge function that would work in this case (All else being equal, we might as well keep the existing entry to avoid paying for the replacement): 

```java
    @Test
    public void regionByCity() {
        final Map<String, String> regionByCityMap = INPUT_ADDRESS_LIST.stream().collect(Collectors.toMap(Address::getCity, Address::getRegion, (a1, a2) -> a1));
        assertEquals(TARGET_REGION_BY_CITY_MAP, regionByCityMap);
    }
```  
  &nbsp;

{: .box-note}
 **Moral #1**: Merge functions exist.  
 The default merge function in Collectors.toMap() is a throwing merger.   

  &nbsp;
  &nbsp;
	
	
## 2.

```java
    @Test
    public void roadByCityAndRegion() {
        final Map<String, Map<String, List<String>>> roadsByCityAndRegionMap = null; //INPUT_ADDRESS_LIST.stream().collect(/**/);
        assertEquals(TARGET_ROADS_BY_CITY_AND_REGION_MAP, roadsByCityAndRegionMap);
    }
```

Most books and tutorials will mention that Collectors.groupingBy() accepts a 'downstream' collector, but then every example will always be Collectors.toList().  

This test intents to help you internalize how to chain downstream collectors together.   

We need to construct this nice structure: `Map<String, Map<String, List<String>>>`, so let's begin!  
	
The first step is to notice that we have a `Map<X, Collection>` that means we need a groupingBy().  

`Collectors.groupingBy(Address::getRegion, /**/)`

	
With the first layer dealt with, we can notice that *again* we find ourselves having to construct a `Map<X, Collection>`
	
```
Collectors.groupingBy(Address::getRegion, Collectors.groupingBy(Address::getCity, /**/))
```   
	
Now if we just fill in Collectors.toList() as the terminal collector, that would leave us with a `Map<String, Map<String, List<Address>>>`, which is close, but not what we want.  

If only there was a collector that allowed us to map Address -> String!  

And with that insight we can arrive at the final implementation: 

```java
    @Test
    public void roadByCityAndRegion() {
        final Map<String, Map<String, List<String>>> roadsByCityAndRegionMap = INPUT_ADDRESS_LIST.stream().collect(Collectors.groupingBy(Address::getRegion, Collectors.groupingBy(Address::getCity, Collectors.mapping(Address::getRoad, Collectors.toList()))));
        assertEquals(TARGET_ROADS_BY_CITY_AND_REGION_MAP, roadsByCityAndRegionMap);
    }
```  

  &nbsp;

{: .box-note} 
**Moral #2**: Collectors can be chained.  
To understand this point you need some practice chaining them
