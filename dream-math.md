
# Part #1: The Original Accusation

On December 11 2020, the speedrun.com Minecraft moderator team released a
    YouTube video analyzing Dream's speedruns of Minecraft 1.16 Java Edition
    done in October.
    [show video website]

Speedrunners of 1.16 need to acquire around 10-to-15 eyes of ender,
    which can be crafted by combining 10-to-15 ender pearls
    with the same number of blaze rods.
    Ender pearls can be obtained fastest by trading with piglins:
    Each trade consumes one gold ingot and has a 4.7% (20-in-423) chance
    of dropping uniformly between 4 and 7 pearls.
    [show MC wiki]
    [show video of filling up ender portal and trades!!]

[[CORRECTION:
    Eyes of ender are crafted from ender pearls and blaze POWDER.
    Each blaze rod can be crafted into 2 blaze powder,
    so 10-to-15 ender pearls and 5-to-7 blaze rods are needed.
]]

The issue is that in Dream's 22 runs over 6 streams, he received
    42-out-of-262 successful pearl trades, or a rate of 16.0%.
    Even after removing Dream's near-WR run to account for
    stopping bias, this rate is still 15.4% (40-out-of-259).
    Dream got 3.3 times the pearl trades he should get.
    [show pearl graph]

Similarly, Dream obtained 211 rods from killing 305 blazes,
    which is a rate of 69.1% compared to the expected 50%.
    [show blaze graph]

Now, the thing that dooms Dream here is that he did not get lucky
    in a single good run, but over the course of 22 runs and 262 trades.
    Getting lucky once is entirely reasonable, for example in his near-WR
    run, Dream traded successfully 2-out-of-3 times, which has a chance of
    almost one percent. This is entirely plausible to happen if you run 100
    attempts for example.
    [show calculations]

        >>> p = 20/423
        >>> p*p + p*(1-p)*p + (1-p)*p*p
        0.006495173733147478
    This is 0.65%

However, getting lucky across hundreds and hundreds of events makes it
    incredibly unlikely. In fact the paper gives the naive chance for this
    amount of pearl and blaze luck as 1-in-20-sextillion,
    or 5-times-10-to-the-minus-23 (5 10^-23).
    [paper with underline]

After accounting for potential biases, this number comes out to the
    now-famous 1-in-7.5-trillion that **ANY** world record contender
    would get Dream's luck.
    [paper with underline]



# Part #2: Dream's Response

On December 23, Dream uploaded a video that denies the accusations.

Here we actually have to analyze Dream's response and the expert's
    response separately, as they actually tell very different stories.

Dream's video has the following structure:

1. First: He says he hired a professional statistician to write an analysis
    which finds 1-in-10-million odds, rather than 1-in-7.5-trillion, which
    means the mods were quote "off by 1-in-7.49999-trillion" which he goes
    on about by scrolling through gold blocks through the entire video.
    He glosses over the fact that 1-in-10-million are still incredible odds.
    [gold block scroll]
    [maybe recreate myself?]

2. Second: He mentions how 1-in-a-trillion events happen daily,
    and that someone must get lucky considering there are 126 million
    Minecraft players.

3. Third: He mentions how the expert accounts for barter stopping,
    which Dream says quote "makes the odds **extremely** more likely",
    which I will get to in a second.

4. Fourth: He mentions that he uploaded his world after the speedrun.
    This can be faked and cannot be heavily weighed in his favor.

5. Fifth: He repeatedly attacks the mods for treating him unjustly,
    their quote "inexperience",
    and making him look quote "as guilty as possible".

I really don't want to debate his last point as this invited drama,
    but I think anyone can agree Dream did not make an argument on a
    mathematical level, claiming he is not good at math.
    This is why I'm turning to the expert's report now.



# Part #3: The Expert's Report

In this section I will be going over
    the four major adjustments the expert made
    to the original 7.5-trillion figure.



1. First: Manipulation Targets.

    This is talking about potential
        cherry-picking in the investigation. Since there are many random
        events in a Minecraft speedrun, some of them might look off by chance.
        Dream put out a document listing 37 RNG manipulation targets that
        are quote "important and comparable to blaze rod drop percentage".
    Blaze drops aren't too important for a run as blazes spawn in packs
        and already have high drop rates.
    Dream's analysis ignores that pearl trades are
        much more significant to the run.
    [show pdf]

    Changing the number of manipulation target from 10 to 37
        means the final result is reduced by a factor of 1332 (37-times-36)
        rather than 90 (10-times-9), which changes the result
        by a factor of 14.8.

    ```
        >>> 37*36
        1332

        >>> 10*9
        90

        >>> 1332/90
        14.8
    ```

    This is not significant when dealing with trillionaire odds
        but still blindly accepts a more favorable number put out by Dream.



2. Second: Barter Stopping.

    Dream makes a big point that the mod team
        made a mistake here, and that accounting for barter stopping
        quote "makes the odds **extremely** more likely".

    First, let me try to explain the correction made here.
    Let's take the example I calculated earlier, getting 2-out-of-3 trades.
    There are 3 possible scenarios here:
        - SS: You get both trades directly (S = success, F = failure)
        - SFS: You fail the second trade
        - FSS: You fail the first trade

    ```
                    _
                -       -
            F               S
          /              /       \
        FS             SF         SS
         |              |
        FSS            SFS
    ```

    The final probability is then the sum of these 3 paths.
    This is 0.65% here:

    ```
        >>> p = 20/423

            SS    SFS         FSS
        >>> p*p + p*(1-p)*p + (1-p)*p*p
        0.006495173733147478

    This is 0.65%
    ```

    The original report modeled this with a binomial distribution,
        where we get 2 out of 3 events with the pearl drop probability 20/423.
        This gives us a different result of 0.64%:

    ```
    General formula:
        >>> 3 * p**2 * (1-p)
        0.006389475218368837
    This is 0.64%

    Specific:
        >>> p*p*(1-p) + p*(1-p)*p + (1-p)*p*p
        0.006389475218368837
    This is identical
    ```

    This difference is because SS is "mistakenly" treated as SSF
        by the binomial model.

    ```
    With barter stopping:
        P = p(SS)  + p(SFS) + p(FSS) = 0.65%

    Without barter stopping (binomial model):
        P = p(SSF) + p(SFS) + p(FSS) = 0.64%
    ```

    As you might have noticed, the difference between both results is pretty
        small. And it does not get much better with more trades.

    The expert included Python code for modelling this which I'm using.
        Re-running the simulation to account for barter stopping
        (always_use_binomial=True in my code)
        only makes it 28% less likely the probabilities were unmodified!
    [[CORRECTION:
        always_use_binomial=True uses the binomial distribution
        always_use_binomial=False uses barter stopping
    ]]

    ```
        >>> 3.12645503e-05 / 2.44534082e-05
        1.278535492651695

    This is +28%
    ```

    The expert included a graph which shows this difference.
        The blue curve shows that runs with 25-or-fewer gold are more likely
        than the binomial distribution predicts.
    [[CORRECTION:
        This should read "around 40-or-fewer"
    ]]
    This graph is misleading to me as it is per-run and not for all runs
        combined, thus you cannot find this 28% figure inside of it, I
        had to modify the code and run the simulation again.
    [[CUT:
    From just the graph I would have assumed the error would be a factor of 2
        per run, thus 2-to-the-22 (2^22) (4 million) in total,
        but this is not the case.
    ]]
    [show Figure 1]

    The original report specifically mentioned that
        the binomial distribution is only a (good) approximation
        of the true probabilities.
    Additionally, the report already said it accounted for it,
        although I did not look into this, as once again we are talking about
        **a 28% difference which has absolutely no bearing on the result**.

    For blaze rods, the expert went with the original investigation's
        binomial model, as the difference would have been even slighter.



3. Third: Inclusion of 17 Old Runs

    Rather than just including Dream's 6 streams (22 runs) after his return to
        speedrunning on October 4 2020, the expert also includes
        5 old streams (17 runs), which he says Dream provided him with.
        [show own screenshot]

    Dream received slightly bad pearl luck in those, that was completely
        consistent with chance. Dream was trading successfully 3.4%
        of the time (expected is 4.7%).
        His overall drop rate comes down from 16.0% to 8.7%,
        which is much closer to the expected 4.7%.

    **This is completely invalid, so much that it seems actually manipulative
        to me.** The expert is no longer investigating whether Dream started
        cheating after returning to speedrunning, but whether he has always
        cheated in every attempt he ever made.
        This is much harder to prove, as the expert also has to prove cheating
        in the 17 old runs where everyone agrees Dream did not cheat.

    Using the expert's numbers, this changes the resulting probability of
        his pearl luck **from about-1-in-a-billion to 1-in-40,000.**
        [beat]
        Combined with his blaze luck this improves Dream's chances
        **from 1-in-quadrillions to 1-in-100-million.**
        [beat]
        This is an "astronomic" difference of a factor of 10,000,000 and the
        main reason the expert comes to a different conclusion.
        [section 9]

    [[NOTE:
        The paper above says 1-in-4,000
        I re-ran his code and got 1-in-40,000
        This might be a simple comma error in favor of Dream (10x)
    ]]

    The experts then handwaves 1-in-100-million to 1-in-10-million,
        which is the number that the abstract and Dream cite.
        [underline handwaving]

    [[NOTE:
        I don't understand his reasoning here.
        He brings the number down by 10x as Dream could have modified
            "in between any stream", but then applies it to the
            "at the beginning of all eleven streams" case
    ]]

    The expert has done Dream a huge favor by trying to prove Dream cheated
        in 17 unrelated runs, bringing the overall odds Dream would achieve
        this streak down to 1-in-10-million.
        But that is STILL pretty unlikely! 1-in-10-million!
        Why does the expert think Dream could achieve 1-in-10-million luck?



4. Fourth: Estimating the Number of Total Streams

    Here is how rare events happen: If you try many times.
        The chance of getting 10 or more eyes of ender in an end portal is
        1-in-200-million. If you do millions of runs in a row however,
        it becomes plausible that any one of those million runs get it.
        This requires the 199,999,999 failed runs to happen however.

    This is a direct quote from the expert's paper:

        Let’s instead suppose that there are 300 livestream speedruns posted
        per day. This is based on perusal of the recordboard at [link] which
        shows that new records within the top 1000 runs happen **about once a
        month, i.e., 30 per day**. There are likely at least 10 times as many
        livestreams as there are record-holders each day, giving us 300
        livestream runs per day and thus 10-to-the-5 livestream runs per year.

    Obvious things out of the way: The expert says one-per-month is 30-per-day,
        this is a simple error, but this doesn't change the fact that his
        estimate is way off here.

    The expert is claiming here that there are 100,000 1.16 **world record**
        (not casual) attempt **39-run-streaks** on stream **each year**

    Since there are so many (100,000) attempts and 1-in-10-million odds,
        the odds of this happening to quote "any one person" is 1-in-100.
        A 1-in-100 chance is entirely plausible, this is about the
        chance of rolling three 6s in a row.
        [beat]
        Thus Dream is exonerated.
        Or is he?

    One problem is that 100,000 is IMO a huge overestimate!
        Of the top 10 1.16 runners on speedrun.com (times below 19 minutes),
        5 of those have a top 20 spot on the 1.9 - 1.15 leaderboard as well!
        (The other 5 have submitted no times to the other leaderboard).
        **The circle of streamers that actively run 1.16 and could plausibly
        almost-world-record is very small.**
        If you only consider sequences of 39 runs that occurred after a break
        and while streaming that number becomes even lower.
        [show leaderboards]

    [[NOTE:
        -          100,000 stream sequences (10\*\*5)
        -  1-in-10,000,000 odds
        => 1-in-100

        He is counting stream sequences rather than stream "careers" here,
        even though he insisted on analyzing Dream's whole career earlier
    ]]

    I'm surprised the expert just handwaves this 100,000 number
        in a single short paragraph, considering that 5 pages and a lot of
        effort is devoted to pointing out a 28% difference due to
        barter stopping.

    The original report accounted for this with a factor of 1,000
        (which I believe to already be somewhat _generous_),
        so the expert uses an adjustment factor of 100 here.



# Part #4: Conclusion

The original report states that the chance that **any 1.16 speedrunner ever**
    could experience Dream's luck is "conservatively estimated" around
        1-in-7.5-trillion

The expert report makes minor adjustments to
        - manipulation targets (14.8x)
        - barter stopping (1.28x)
        - relevant streams (100x) and
        - other minor factors (4x)
    which improve Dream's odds by around 7,500x.
    The original estimate of 1-in-7.5-trillion becomes 1-in-a-billion.

[[CORRECTION:
    The missing 4x factor seems to come from the difference of using
        a binomial model per run (3 choose 2, then 10 choose 2, then ...)
        vs using a binomial model for all runs combined (620 choose 54).

    This means his adjustment to the bartering model are 5x (4x times 1.28x)
        in total.
]]

The expert then includes 17 of Dream's old "clean" runs
    to his 22 new "sus" runs to reduce his chance by a factor of
    10,000,000x.
    This reduces the odds of this streak happening to any runner, at all, ever,
    to 1-in-100.
    At this point the expert declares, quote

        The probabilities are not so extreme as to completely rule out any
        chance that Dream used the unmodified probabilities

I hope I was able to give an accurate and insightful summary of the expert
    report, especially to non-technical audiences.
    **The key takeaway is that the expert achieved a much more favorable
    number by including 17 old runs with below-average pearl luck.**

I consider this wildly inappropriate as we are not proving if he
    cheated in all 39 runs (which is hard to prove, as we have to prove
    cheating on 17 pre-hiatus runs that are believed to be "clean").

Additionally, the selection of these 6 "sus" streams has already
    been accounted for by the handwaved "100,000" number of possible
    streaks that could have been investigated.

[[CORRECTION:
    The selection of the 6 "sus" streams out of 11 has been explicitly accounted
        for in the original paper with a factor "11(11+1)/2" (= 66).
    (This is the number of ways one can pick contiguous subsequences of
        11 streams)

    Given legitimate data, any of these sequences would pass all statistical
        tests when accounted for with this factor.

    The expert's insistence on including all 11 streams is not valid.
]]

262 barters and 305 blaze kills across 22 runs are enough
    of a sample size to make a conviction **beyond any reasonable doubt**.

**Dream achieved his near-world-record run on an either
    intentionally or unintentionally modified copy of Minecraft 1.16.**


I posted the expert's code online, since it was poorly formatted
    and not copy-pastable.
    The expert did not provide the data he used for blaze rod calculations.
    [[CUT:
        Missing is blaze data for old streams, which he got directly from Dream.
    ]]
    [show video of copy-pasting]
    [show video of relevant gist]


Alright, thanks for watching, new to this YouTube thing,
    if this video does well I might post more stuff.





