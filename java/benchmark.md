# 基准测试

>`java 17`和`jmh 1.36`

## pom.xml

```xml
<!--
Copyright (c) 2005, 2014, Oracle and/or its affiliates. All rights reserved.
DO NOT ALTER OR REMOVE COPYRIGHT NOTICES OR THIS FILE HEADER.

This code is free software; you can redistribute it and/or modify it
under the terms of the GNU General Public License version 2 only, as
published by the Free Software Foundation.  Oracle designates this
particular file as subject to the "Classpath" exception as provided
by Oracle in the LICENSE file that accompanied this code.

This code is distributed in the hope that it will be useful, but WITHOUT
ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or
FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License
version 2 for more details (a copy is included in the LICENSE file that
accompanied this code).

You should have received a copy of the GNU General Public License version
2 along with this work; if not, write to the Free Software Foundation,
Inc., 51 Franklin St, Fifth Floor, Boston, MA 02110-1301 USA.

Please contact Oracle, 500 Oracle Parkway, Redwood Shores, CA 94065 USA
or visit www.oracle.com if you need additional information or have any
questions.
-->

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.sample</groupId>
    <artifactId>test</artifactId>
    <version>1.0</version>
    <packaging>jar</packaging>

    <name>Auto-generated JMH benchmark</name>

    <prerequisites>
        <maven>3.0</maven>
    </prerequisites>

    <dependencies>
        <dependency>
            <groupId>org.openjdk.jmh</groupId>
            <artifactId>jmh-core</artifactId>
            <version>${jmh.version}</version>
        </dependency>
        <dependency>
            <groupId>org.openjdk.jmh</groupId>
            <artifactId>jmh-generator-annprocess</artifactId>
            <version>${jmh.version}</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <jmh.version>1.36</jmh.version>
        <javac.target>17</javac.target>
        <uberjar.name>benchmarks</uberjar.name>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <compilerVersion>${javac.target}</compilerVersion>
                    <source>${javac.target}</source>
                    <target>${javac.target}</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.2</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <finalName>${uberjar.name}</finalName>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>org.openjdk.jmh.Main</mainClass>
                                </transformer>
                            </transformers>
                            <filters>
                                <filter>
                                    <!--
                                        Shading signed JARs will fail without this.
                                        http://stackoverflow.com/questions/999489/invalid-signature-file-when-attempting-to-run-a-jar
                                    -->
                                    <artifact>*:*</artifact>
                                    <excludes>
                                        <exclude>META-INF/*.SF</exclude>
                                        <exclude>META-INF/*.DSA</exclude>
                                        <exclude>META-INF/*.RSA</exclude>
                                    </excludes>
                                </filter>
                            </filters>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
        <pluginManagement>
            <plugins>
                <plugin>
                    <artifactId>maven-clean-plugin</artifactId>
                    <version>2.5</version>
                </plugin>
                <plugin>
                    <artifactId>maven-deploy-plugin</artifactId>
                    <version>2.8.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-install-plugin</artifactId>
                    <version>2.5.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-jar-plugin</artifactId>
                    <version>2.4</version>
                </plugin>
                <plugin>
                    <artifactId>maven-javadoc-plugin</artifactId>
                    <version>2.9.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-resources-plugin</artifactId>
                    <version>2.6</version>
                </plugin>
                <plugin>
                    <artifactId>maven-site-plugin</artifactId>
                    <version>3.3</version>
                </plugin>
                <plugin>
                    <artifactId>maven-source-plugin</artifactId>
                    <version>2.2.1</version>
                </plugin>
                <plugin>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>2.17</version>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>

</project>
```

## code

圆周率计算

```java
/*
 * Copyright (c) 2005, 2014, Oracle and/or its affiliates. All rights reserved.
 * DO NOT ALTER OR REMOVE COPYRIGHT NOTICES OR THIS FILE HEADER.
 *
 * This code is free software; you can redistribute it and/or modify it
 * under the terms of the GNU General Public License version 2 only, as
 * published by the Free Software Foundation.  Oracle designates this
 * particular file as subject to the "Classpath" exception as provided
 * by Oracle in the LICENSE file that accompanied this code.
 *
 * This code is distributed in the hope that it will be useful, but WITHOUT
 * ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or
 * FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License
 * version 2 for more details (a copy is included in the LICENSE file that
 * accompanied this code).
 *
 * You should have received a copy of the GNU General Public License version
 * 2 along with this work; if not, write to the Free Software Foundation,
 * Inc., 51 Franklin St, Fifth Floor, Boston, MA 02110-1301 USA.
 *
 * Please contact Oracle, 500 Oracle Parkway, Redwood Shores, CA 94065 USA
 * or visit www.oracle.com if you need additional information or have any
 * questions.
 */

package org.sample;

import org.openjdk.jmh.annotations.*;

import java.math.BigDecimal;
import java.math.RoundingMode;
import java.util.concurrent.TimeUnit;

public class BenchmarkDemo {

    private static final int N = 60_000; // 小数点后位数

    @Benchmark
    @BenchmarkMode({Mode.AverageTime, Mode.Throughput})
    @Fork(value = 2)
    @Threads(2)
    @OutputTimeUnit(TimeUnit.MILLISECONDS)
    @Warmup(iterations = 1, time = 2)
    @Measurement(iterations = 1, time = 4)
    public void piCalculation() {
        BigDecimal part1 = arc_tan(57).multiply(new BigDecimal(176));
        BigDecimal part2 = arc_tan(239).multiply(new BigDecimal(28));
        BigDecimal part3 = arc_tan(682).multiply(new BigDecimal(-48));
        BigDecimal part4 = arc_tan(12943).multiply(new BigDecimal(96));

        BigDecimal part = part1.add(part2).add(part3).add(part4);
    }

    public static BigDecimal arc_tan(int x) {
        // 为了防误差
        int n2 = N + 2;

        BigDecimal result = BigDecimal.ZERO;
        BigDecimal x_square = new BigDecimal(x * x);
        BigDecimal big_x = new BigDecimal(x);
        BigDecimal temp;
        BigDecimal res = BigDecimal.ONE.divide(big_x, n2, RoundingMode.HALF_EVEN);

        boolean b = true;
        for (int i = 1; ; i += 2) {
            temp = res.divide(new BigDecimal(i), n2, RoundingMode.HALF_EVEN);
            // 根据莱布尼兹级数结果=0时返回
            if (temp.compareTo(BigDecimal.ZERO) == 0) {
                break;
            }
            if (b) {
                result = result.add(temp); // 加
            } else {
                result = result.subtract(temp); // 减
            }
            b = !b;
            res = res.divide(x_square, n2, RoundingMode.HALF_EVEN);
        }
        return result;
    }
}
```

Last Modified 2023-02-16
