---
layout: post
author: Luís Cruz
title: "Green Software Engineering Done Right: a Scientific Guide to Set Up Energy Efficiency Experiments"
image: "img/blog/2021-08-20/og_image.png"
show_image: True
mermaid: True
equation: True
summary: "Are you studying the energy consumption of software technologies? Regardless of whether you are focusing on blockchain, artificial intelligence, cloud systems, or any other type of software, it is important to define a reliable approach before diving deep into energy measurements. This post provides a step-by-step guide to help you define a solid scientific methodology to measure software energy consumption. 
A simple mistake in the methodology might completely void your results and increase the chances of drawing wrong conclusions. We will see how to mitigate any potential bias or errors in the energy measurement setup – this is quintessential if you are planning to publish your results. If you have a general interest in the energy efficiency of software but are not exactly doing research, you may also find this post interesting. Come along and let me know your thoughts."
redirect_from:
  - /2021/08/20/scientific-guide.html
bibtex: |-
  @misc{cruz2021green,
    title = {Green Software Engineering Done Right: a Scientific Guide to Set Up Energy Efficiency Experiments},
    author = {Lu\'{i}s Cruz},
    year = {2021},
    howpublished={\url{http://luiscruz.github.io/2021/10/10/scientific-guide.html}},
    note = {Blog post.}
  }
---

<span class="first-letter">I</span>n my [previous post](/2021/07/20/measuring-energy.html), we have covered tools to measure the energy consumption of a computer during a particular period of time. Although these tools provide a reliable indication of the energy consumption, using it as part of a scientific study is far from trivial.

Even when replicated in the exact same computer, energy measurements are affected by many different factors: hardware temperature (CPU, GPU, memory, etc.), room temperature, background tasks running in the system, thread scheduling, and so on.

This type of uncertainty in measuring energy consumption creates major difficulties when assessing the energy consumption of a particular version of a software project. For example, energy tests that rely on a single measure will have a **high tendency of being flaky** – i.e., multiple executions of the same test will fail to produce the same result.

This problem takes another level if we are counting on these measurements to make **groundbreaking research contributions** in this area. Some research projects in the past have underestimated this issue and failed to produce replicable findings. Hence, this article presents a roadmap on how to properly set up a scientific methodology to run energy efficiency experiments. It mostly stems from my previous work on [doing research and publishing](/publications) on Green Software.


This article is divided into two main parts: 1) how to set up energy measurements with minimum bias, and 2) how to analyse and take scientific conclusions from your energy measurements.
Read on so that we can get your paper accepted in the best scientific conference.

--- 
#### 👉 Note 1:
If you are a **software developer** enthusiastic about energy efficiency but you are not particularly interested in scientific experiments, this article is still useful for you. It is not necessary to do "everything by the book" but you may use one or two of these techniques to reduce the likelihood of making wrong decisions regarding the energy efficiency of your software.

--- 

## Unbiased Energy Data ⚖️

There are a few things that need to be considered to minimise the bias of the energy measurements. Below, I pinpoint the most important strategies to minimise the impact of these biases when collecting the data.

### Zen mode 🧘🏾‍♀️

The first thing we need to make sure of is that the only thing running in our system is the software we want to measure. Unfortunately, this is impossible in practice – our system will always have other tasks and things that it will run at the same time. Still, we must at least minimise all these competing tasks:

- all applications should be closed, notifications should be turned off;
- only the required hardware should be connected (avoid USB drives, external disks, external displays, etc.);
- turn off notifications;
- remove any unnecessary services running in the background (e.g., web server, file sharing, etc.);
- if you do not need an internet or intranet connection, switch off your network;
- prefer cable over wireless – the energy consumption from a cable connection is more stable than from a wireless connection.

### Freeze your settings 🥶

It is not possible to shut off the unnecessary things that run in our system. Still, we need to at least make sure that they will behave the same across all sets of experiments. Thus, we must fix and report some configuration settings. One good example is the brightness and resolution of your screen – report the exact value and make sure it stays the same throughout the experiment. Another common mistake is to keep the automatic brightness adjustment on – this is, for example, an awful source of errors when measuring energy efficiency in mobile apps.

### Warm-up 📶

Energy consumption is highly affected by the temperature of your hardware. The higher the temperature, the higher the resistance of electrical conductors, leading to higher dissipation and consequently more energy consumption. If we start measurements right after the workstation comes back from sleep, it will probably be relatively cool. As soon as we start executing our energy tests, the temperature of the computer will rise until it reaches a plateau. This means that the first measurements will have less energy consumption for the sole reason of having been the first ones.

Typically, it is recommended to **run a dummy task before start measuring energy consumption**. This could take as long as **five minutes** and could be done by executing a CPU-intensive task, such as the Fibonacci sequence. Alternatively, my favourite approach is to run the same energy tests and **discard results in the end**.

<p class="lead"><i>Run energy tests a few times before keeping track of the measurements.</i></p>

### Repeat 🔁

The best way to make sure your measurements are reliable is by performing statistical hypothesis testing (more about this below). Mostly, it implies that we need to repeat measurements a significant number of times. **The magic number for the repetition of measurements is 30.** This is currently the accepted sample size to make sure we have enough data to conduct a valid analysis. Less than 30 would also be acceptable, but it would be a pity if our results were not statistically significant for the mere reason that there were not enough data points. 

### Rest ⏸

Imagine that we repeat the same experiment 30 times with no rest in between them. Our CPU will probably be warmer in the last experiment than in the first one. It is important to make sure that all measurements are executed under the same conditions. Hence, it is common practice to do a pause/sleep between executions. **There is no golden rule but one minute should be enough**. It can be more or less depending on your hardware or the duration of your energy test.

<p class="lead">Give it a one-minute sleep between measurements.</p>

### Shuffle 🔀

It is not a mystery that energy consumption depends on so many factors that it is impossible to control all of them. If you are comparing two software versions, `A` and `B`, and execute version `A` 30 times followed by `B` another 30 times, there are chances that between the first and the second half of measurements, the factors affecting the energy consumption have changed. Hence, the energy consumption of the two versions will be different also because the context has changed. To mitigate this issue it is important to **mix the executions of the different versions**.

But we don't stop there. In some mysterious cases, it might happen that after finishing the execution of version `A` the system is more likely to execute a particular background task than after version `B` (e.g., freeing up memory in the swap). If most measurements of version `A` happen after `B`, they will also be affected by the side effects of running `B`. In sum, there will be a bias in the measurements of `A` that has nothing to do with the execution of `A`. To mitigate this bias, we randomly pick the version to be executed so that after each execution, the next one can equally be `A` or `B`.

(PS: I hope I did not confuse you with so many `AB`'s 🤓)

### Keep it cool 🌡

We have already settled that temperature affects energy consumption. Hence, we should not let the differences in the room temperature affect our measurements.
In scientific research, it is very common that experiments have to run over a few days. This means that some measurements will happen both during the night, with lower temperatures, and during the day, with higher temperatures. This bias has to be avoided at all costs.

**Always make sure there is a stable room temperature**. This is straightforward if the room where you are running the experiments is properly climatized. In case this is not possible, there is still an acceptable solution: collect the temperature at the same time you collect energy consumption data. Most computers already provide this information, so you can automatically log this data along with the rest of the energy results. Later on, discard measurements that were not executed under the same temperature. This is not ideal, as probably you will have to rerun your experiments multiple times before you get enough valid measurements. Still, it works and the results of your research paper are equally valid.

### Automate Executions 🤖

Since we are repeating experiments 30 times, we need to make sure those 30 experiments are replicable in the exact same way. Moreover, it would be unfeasible to replicate all these experiments manually.
For this purpose, we use a **software testing library to reproduce a realistic use case scenario** that we want to measure. This could be a sequence of calls to API methods in the software, or even a sequence of interactions with the graphical user interface. There are plenty of software testing platforms that could fit this purpose. Depending on the programming environment, select one that fits your case. A few examples include [JUnit](https://junit.org), [Espresso](https://developer.android.com/training/testing/espresso), [pytest](https://docs.pytest.org/), etc.


## Energy Data Analysis 📊

After having all the data collected, there is still quite some work to do before we can jump to conclusions. The most obvious approach would be that we compute and compare the means of each 30-size sample. That's not too far from reality but there are still a few steps we need to make before we can reliably use mean differences. In particular, we need to account for the existence of errors and that these errors may not affect the measurements of two different versions in the same way. Hence, we perform an exploratory analysis on the samples to **discard unexpected biases** and then **perform hypothesis testing** to finally compare results.

### Investigating unexpected biases

We need to investigate whether there were unexpected errors during measurements. 
Despite the meticulous preparation to set up a reliable measurement setup, there are still unexpected events that can randomly appear and ruin our measurements.
Hence, now that we have all data, we need to search for measurements that are not representative of a common execution of the software. For example, it is quite common that, somewhere amongst the 30 executions, there is one or two that failed to finish due to some unexpected error – consequently, the execution is shorter and spends less energy – falsely appearing as more energy efficient. In other cases, it could happen that the system executed an unexpected task that seldom happens and we did not anticipate. We need to get rid of all these samples since they create unwanted bias in our results.

There are a few strategies to detect and mitigate these errors. The first setup is to create a plot of the distribution of each sample – i.e., the distribution of the energy consumption of each software version. My favourite plot for this purpose is a mix of a violin and a box plot.

![Violin plots](/img/blog/2021-08-20/normal_data.svg){: class="center-block" width="500px" }
<p class="text-center text-muted"><small markdown="span">Plot of the distribution of energy consumption for versions `A` and `B`. [View source][Plots source].</small></p>

The plot above shows that the distributions have a bell shape. By looking at the plots we can say that most likely the samples follow a normal distribution. This is exactly how we want our energy data to look.
Now imagine that we have a few data points that deviate from the others. The shape of the distributions could start looking somehow like this:

![Violin plots](/img/blog/2021-08-20/paranormal_data.svg){: class="center-block" width="500px" }
<p class="text-center text-muted"><small markdown="span">Plot of the distribution for versions `A` and `B` when there were a few unreliable measurements. [View source][Plots source].</small></p>

In version A, this new figure shows that the distribution has two clear peaks: one around 100 Joules and another around 80 Joules. There were probably some unexpected differences in the executions of these measurements. It is important to investigate these differences before making a judgement on the real energy consumption of versions `A` and `B`.

In version `B`, the figure shows that there are two outliers, highlighted in red, that clearly deviate from the rest of the data points. It is also important to investigate why these measurements were so different.

The problem when your distribution is not normal is that we cannot confidently say that the errors that affected the measurements of version `A` were equally affecting version `B`.

Hence, the question we need to make is: why are these measurements deviating from the normal distribution? There is a myriad of potential explanations, but there are a few that happen 99% of the time:

1. **Your tests are not fully replicable** or are not deterministic. This is particularly common in user interface tests. It could happen for example that your user interface takes longer to refresh and the rest of the test will behave differently because the expected interface elements were not available at the right time. This is also frequent with network requests. 
2. **There was an error in some of the executions**. This means that a few particular measurements deviated from the others because there was some exception being raised. This is similar to the previous one but somehow easier to detect: often, these measurements have oddly small energy consumptions. If not detected, this executions might give us the wrong impression that a given version is more energy efficient.
3. **There was an unusual task being run by the system** or another application at the same time as the execution. It could happen, for example, that the system received a notification and reacted to it. It is important that all notifications are muted and that there are no other applications running at the same time. However, there will always be an unexceptional case that we did not consider. For example, next thing you know, your system opens Microsoft AutoUpdate, or another user logs in your system, or someone inserts new hardware at some point, and so no.
4. **Your computer entered a different power mode**. Modern systems have all kinds of mechanisms to optimise the battery life and performance of your computer. Worst case scenario, in the middle of the execution your computer decided to enter a sleep mode. If your measurement did not break, it probably took more time than it should. Many other exceptional behaviours can happen, and they all need to be discarded if we want to have reliable measurements that can be used for comparison.
5. **External physical conditions have changed**. Despite the hassle to control the temperature and other external factors, there are still unexpected variables that may disruptively affect energy consumption. For example, someone opened a window in the middle of your experiments. From that point on all the measurements will have a slight change. If that change is too big the distribution of measurements will no longer be normal.
6. **Any paranormal phenomena 👻**. Even if you cannot explain it, if it's not normal, don't trust it.

Drawing the plots is the easiest way to get some intuition on whether the distribution is normal. Yet, that can be disputable and you don't want the reviewers of your paper raising second thoughts about it. Hence, use the well-reputed statistic test for normality – [Shapiro-Wilk test](https://en.wikipedia.org/wiki/Shapiro–Wilk_test). In short, all your samples should yield a $p$-value above $0.05$.

### What to do if the samples are not normal? 

In case some of your samples do not follow a normal distribution. We have two options:

1. **Repeat the experiments**. Preferably, start by finding out what happened (the time of execution may help) and fix the issue. We don't want to deal with the same issues all over again in a second (or third!) round.
2. **Remove outliers**. If you suspect that there were only a few data points deviating from the rest of the sample, you can perform the z-score method to detect and remove outliers.

To perform outlier removal, we remove all data points that deviate from the mean more than 3 standard deviations – i.e., $\left\| \bar{x}-x\right\| > 3s$, where $\bar{x}$ is the sample mean, $x$ is the value of the measurement and $s$ is standard deviation of the sample.

If you perform outlier detection, I recommend to do it for every sample of measurements, for consistency. Otherwise, you might be accused of cherrypicking: i.e., only performing outlier removal in the cases that support exciting results.

One side effect of removing outliers is that you will no longer have 30-size samples. That is still okay for the kind of analysis that we want to do with our energy data. Anything above 25 measurements should be fine. If you end up with less than that – for example, you have more than 5 outliers – you should check what went wrong and seriously consider rerunning the experiments.

### Statistical Significance 

Now that we have finally been able to collect reliable measurements, it is time to compare the energy consumption of our samples.
The most obvious way is to compare the means of the samples. If energy consumption of `A` is bigger than `B` then `A`, on average, ***was*** less energy efficient. That's true. Yet, as researchers, we want to make sure our results generalise: we want to say that `A` ***is*** less energy efficient. In other words, we want to make sure that if we repeat our measurements, we will most likely repeat the same conclusion that `A` is less energy efficient – despite the potential errors implicit in the measurement of energy.

The common scientific approach to assess whether results are replicable is by performing statistical significance testing. Since our data follows a normal distribution, I typically use the two-tailed parametric test [Welch's t-test](https://en.wikipedia.org/wiki/Welch%27s_t-test) with a significance level of $\alpha = 0.05$.

We can formulate our hypothesis test as follows:

$H_0$: The means of energy consumption of versions `A` and `B` are equal.

$H_1$: The means of energy consumption of versions `A` and `B` are different.

Where $H_0$ is the null hypothesis and $H_1$ is the alternative hypothesis.
Hence, to come down to the conclusion that `A` is more or less efficient than `B` we need to reject the null hypothesis. In other words, the $p$-value needs to be less than $0.05$.

--- 
#### 👉 Note 2: 
**Avoid using the popular [Student's t-test](https://en.wikipedia.org/wiki/Student%27s_t-test)** for significance testing with energy consumption measurements. It has the underlying assumption that the population variances are equal. This is not necessarily assured in our experimental setup. The good news is that Welch's t-test does not rely in such assumptions and it has almost the same statistical power.

--- 
#### 👉 Note 3: 
**You may find some research studies that use non-parametrical tests.** Non-parametrical tests, such as the well-known [Mann–Whitney *U* test](https://en.wikipedia.org/wiki/Mann–Whitney_U_test), can be used without making any assumption about the shape of the distribution. Hence, this is commonly used when the collected energy measurements do not yield a normal distribution. One of the issues of using a non-parametric test is that it might have a **higher type II error rate** -- i.e., mistakenly accepting the null hypothesis. This means that the test is less likely be able to find statistical significance in the difference between two versions of the software. But, there is another issue that is more important than that: **why is the data not normal in the first place?** Probably, **there are a few low-quality energy measurements that should be fixed** in the first place.

---

### Effect size

So now that we scientifically proved that the energy consumption is indeed different, we can now analyse the mean difference between the two versions. It is always nice to report the mean difference, computed as follows:

$$\Delta \bar{x} = \bar{x}_A - \bar{x}_B$$

I suggest you look into other effect-size measures, for example, Cohen's-*d*, which provides a better idea of whether the mean difference is small, medium, or large, by considering the standard deviation as well.

Nevertheless, using statistical metrics to measure effect size is not enough – there should be a discussion of the **practical effect size**. More important than demonstrating that we came up with a new version that is more energy efficient, you need to demonstrate that the benefits will actually be reflected in the overall energy efficiency of normal usage of the software. For example, imagine that the results show that a given energy improvement was only able to save one joule of energy throughout a whole day of intensive usage of your cloud software. This perspective can hardly be captured by classic effect-size measures. The statistical approach to effect size (e.g., mean difference, Cohen's-*d*, and so on) is agnostic of the context of the problem at hand.

Unfortunately, there is not a specific fancy metric for the practical effect size. But of course, your paper should never miss this kind of critical analysis in your research paper. It usually fits nicely the discussion section of your research paper.


## Wrap-up 

That's all, folks! In this article, we have seen how to create an experimental methodology to reliably collect energy data and to derive scientific conclusions from it. We can sum up all the strategies with the following graph:

<div class="mermaid">
flowchart TB
    A["Improve\nEnergy Consumption\n(versions A and B)"]
    A --> C
    subgraph C[Energy Data Collection]
    direction TB
      C1[Zen mode]
      C2[Freeze and report settings]
      C3[Warm up your setup]
      C4[Repeat 30 times]
      C5[Sleep between measurements]
      C6[Shuffle measurements]
      C7[Control Room Temperature]
      C8[Create Automated Tests]
      C1 --- C2 --- C3 --- C4 --- C8
      C1 --- C5 --- C6 --- C7 --- C8
    end


    C --> D1
    subgraph D["Energy Data Analysis&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|"]
        D1[Analyse\ndistribution shapes]
        D2[Is data normal?]
        D3[Investigate problems\n in experiments]
        D4[Remove\n outliers]
        D5[Repeat\n experiments]
        D6[Significance tests]
        D7[Effect size]
        D1 --> D2
        D2 -- No! --> D3
        D3 --> D4
        D4 --> D5
        D5 --> D1
        D2 -- Yes! --> D6
        D6 --> D7
    end
    D7 ==> E
    E[Write your Paper!]

    style E fill:lightblue
    style D fill: transparent,text-align:left
    style C fill: transparent
    style A fill:lightblue
</div>


Keep in mind though that there are often many right ways of doing the same thing – even in science.
Unfortunately, my previous research did not always rely on all these strategies. After several years of doing research in this area and studying the state of the art, I came up with this method. I totally recommend anyone starting their research in this field to follow it.

However, you can obviously disagree with it – that would be a nice discussion! The whole field is still relatively new, and there is still a lot to improve along the way. Have no qualms about sharing your opinion and suggesting any changes/improvements. Also **if you have any questions about this topic feel free to drop me an [email](mailto:{{site.email}}) or [connect on Twitter](https://twitter.com/{{site.twitter_username}})**.

### Useful resources 📚

If you want to learn more about this topic, here are some follow-up pointers you should not miss:

- [How to Measure the Energy Consumption of your Software](/2021/07/20/measuring-energy). It provides a list of different tools to estimate the energy consumption of your workstation.
- [Snippet for violin plots][Plots source]. Python Notebook with the code that was used to generate the violin plots in this article.
- [On the Energy Footprint of Mobile Testing Frameworks](/publications/2019-12-cruz-uiframeworks). An example of a research paper in which, together with my Ph.D. supervisor, we have used most of the mentioned guidelines to compare the energy consumption of different UI testing frameworks.
- [Data Analyst Nanodegreen Program](https://www.udacity.com/course/data-analyst-nanodegree--nd002). This nanodegree gave me the basics to start learning more about hypothesis testing and applying it in my empirical research. I do not have any partnership with Udacity – this is the resource that worked for me but I am sure there are free alternatives that are equally useful.
- [Catalog of Energy Patterns](https://tqrg.github.io/energy-patterns/). If you are looking for examples of energy improvements made in software projects we will find several Android instances in this catalog.
- [Sustainable Software Engineering course at the TU Delft](https://luiscruz.github.io/course_sustainableSE/). I teach Sustainable SE to Master-level students at the Delft University of Technology. I recommend taking a look at some of the materials (work in progress).

[Plots source]: https://colab.research.google.com/drive/1DmFuBwhs9wI4_6zaaUh5B1rTiVt-hNt9?usp=sharing
