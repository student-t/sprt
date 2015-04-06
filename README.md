### Overview

SPRT is a simulator for Sequential Probability Ration Test, applied to chess
testing, using the BayesElo model.

### Compiling

* On Linux, simply run `make.sh`
* On other POSIX compliant platforms, I don't expect many problems
* Otherwise, requires a standard compliant C99 compiler (eg. mingw for Windows)

### Syntax

`sprt elo_min elo_max elo_step nb_simu draw_elo elo0 elo1`

* `elo_min`, `elo_max`, `elo_step`: Range of elo values to test.
* `nb_simu`: Number of simulation to run for every step
* `draw_elo`: Parameter of the BayesElo model. To calibrate this value, use the
following formula: `draw_elo = 200.log10[(1-w)/w.(1-l)/l]`, where `w` and `l`
are the win and loss ratio
* `elo0`, `elo1`: Hypothesis H0 and H1 of the test, **expressed in BayesElo**

Note that alpha and beta are always 5%. Do not change these values in the code.
Instead, play with the bounds (elo0,elo1).

Note also that SPRT is an infinite test, although probability of long runs
decays very fast. To perform a truncated SPRT, modify this line of code:

    #define T       128000	// truncate at 128k games

Decision will then be based on which bound is closest to the current LLR
(log-likelyhood ratio).

### Example

	$ ./sprt -2 6 2 10000 250 0 4
	       ELO  BayesElo    P(win)   P(loss)   P(pass) avg(stop)
	     -2.00     -3.23    0.1888    0.1946    0.0007     16960
	      0.00      0.00    0.1917    0.1917    0.0502     39712
	      2.00      3.23    0.1946    0.1888    0.8583     52180
	      4.00      6.45    0.1975    0.1860    0.9990     20017
	      6.00      9.68    0.2005    0.1832    1.0000     11631

For example, +2.00 ELO with draw_elo = 250 corresponds to +3.23 BayesElo, and the
SPRT(elo0=0,elo1=4) test has 85.8% probability to pass.
