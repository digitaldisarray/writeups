# 2024 LACTF rev/the-secret-of-java-island
<strong>The Secret of Java Island</strong> is a 2024 point-and-click graphic adventure game developed and published by LA CTF Games. It takes place in a fictional version of Indonesia during the age of hacking. The player assumes the role of Benson Liu, a young man who dreams of becoming a hacker, and explores fictional flags while solving puzzles.

Files: game.jar

## Solution

When you run the jar with the java 20.0.2 you are prompted with a decision tree. The path to the flag is to do the right "exploit" which in the game is just pressing buttons in the right order (I guess the same could be said about real exploits).

After dropping the jar into a decompiler we find that this is the relevant code:
```java
if (paramInt == 0) {
    exploit += "d";
    story.setText("You clobbered the DOM. That was exploit #" + exploit.length() + ".");
} else {
    exploit += "p";
    story.setText("You polluted the prototype. That was exploit #" + exploit.length() + ".");
}

if (exploit.length() == 8) {
    try {
        MessageDigest messageDigest = MessageDigest.getInstance("SHA-256");
        if (!Arrays.equals(messageDigest.digest(exploit.getBytes("UTF-8")), new byte[] { 
                69, 70, -81, -117, -10, 109, 15, 29, 19, 113, 
                61, -123, -39, 82, -11, -34, 104, -98, -111, 9, 
                43, 35, -19, 22, 52, -55, -124, -45, -72, -23, 
                96, -77 })) {
            state = 7;
        } else {
            state = 6;
        } 
        updateGame();
    } catch (Exception exception) {
        throw new RuntimeException(exception);
    }
}
```

In the game the prompt to either "clobber the DOM" or "pollute the prototype" is repeated until we have clicked either option 8 times.  
Once we click 8 times, our series of exploit choices is compared to a hash.
If our exploit choices match the hash, then we go into the else and our state is set to 6.

When our state is set to 6, a variable called ``hasGlove`` is set to ``true`` and this glove allows us to pull a lever in the game that sends our exploit over to the remote server to check if it is right, and returns us the flag.  

So, we just need to brute force a string that matches the hash. But before we do that lets just generate every single possibility using python because I'm a bit rusty on my java:
```python
from itertools import product

def generate_combinations(length):
    chars = 'dp'
    return [''.join(comb) for comb in product(chars, repeat=length)]

def write_combinations_to_file(combinations, filename):
    with open(filename, 'w') as file:
        for comb in combinations:
            file.write(comb + '\n')

if __name__ == "__main__":
    combinations = generate_combinations(8)
    write_combinations_to_file(combinations, 'combinations.txt')
```

Now that we have used a script to generate every possible 8 character long string with d and p in it, we can write some java that reads it and checks it against the hash:


```java
public static void checkExploit(String exploit) {
    try {
        MessageDigest messageDigest = MessageDigest.getInstance("SHA-256");
        if (Arrays.equals(messageDigest.digest(exploit.getBytes("UTF-8")),
                new byte[] { 69, 70, -81, -117, -10, 109, 15, 29, 19, 113, 61, -123, -39, 82, -11, -34, 104, -98,
                        -111, 9, 43, 35, -19, 22, 52, -55, -124, -45, -72, -23, 96, -77 })) {
            System.out.println("MATCH: " + exploit);
            System.exit(0); // Quit since we found a match
        }
    } catch (Exception exception) {
        throw new RuntimeException(exception);
    }
}

public static void main(String[] args) {
    BufferedReader reader = null;
    try {
        reader = new BufferedReader(new FileReader("combinations.txt"));

        // Go to every line in the file and check if it is a match with the hash
        String line;
        while ((line = reader.readLine()) != null) {
            checkExploit(line);
        }
    } catch (IOException e) {
        System.err.println("Error reading the file: " + e.getMessage());
    } finally {
        try {
            if (reader != null) {
                reader.close();
            }
        } catch (IOException e) {
            System.err.println("Error closing the file: " + e.getMessage());
        }
    }
}
```

When we run that java code we get the output ``MATCH: dpddpdpp``  
So, we can launch the game and click the buttons in the order of dom, prototype, dom, dom, prototype, dom, prototype, prototype which will give us the glove we need to contact the server and get the flag.

After clicking all of the right buttons in the adventure game we get the flag in a popup:
``lactf{the_graphics_got_a_lot_worse_from_what_i_remembered}``
