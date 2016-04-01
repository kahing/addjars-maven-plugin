### The problem ###

Many new maven users often ask the same simple question:
> _I have some third party jars which are not available in maven repositories. How can I add those jars to the project's classpath?_

The question is simple but the suggested solutions are usually imperfect or unreasonably complex.

Some people suggest using dependencies of scope `system`. This solution is simple and works but unfortunately has significant drawbacks (such as not working transitive dependencies etc).

Others say that `system` dependencies must never ever be used. The only **right way** to add third party jars is installing them into your own repository, and then referring to the corresponding artifacts in the `dependencies` section of `pom.xml`. Well, that probably **is** the right solution. Unfortunately it forces developers to put extra efforts into creating and maintaining the repository.

### The solution ###

The `addjars-maven-plugin` is supposed to address this problem. What it does is the following:
  1. Automatically installs the jars into the local repository as separate artifacts.
  1. Automatically adds the artifacts to the list of project's dependencies.
As you can see, it implies the "right way" mentioned above, but automagic is involved in this process.

Please refer to the UsagePage to find out how to use this magic.