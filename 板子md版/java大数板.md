```java
package com.company;
import java.math.BigInteger;
import java.util.*;
import java.io.*;

public class Main{
    public static void main(String[] args){
        Scanner cin = new Scanner(System.in);
        BigInteger mod=new BigInteger("1000000007");
        BigInteger p;
            int n = cin.nextInt();
            BigInteger x = cin.nextBigInteger();
            for(int i = 1; i < n; i++){
                BigInteger y = cin.nextBigInteger();
                p=x.multiply(y).divide(x.gcd(y));
                x=p;
            }
            p=x.remainder(mod);
            System.out.println(p);
    }
}
```

