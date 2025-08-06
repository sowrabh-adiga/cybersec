# cybersec
Collection of implementation of hardware and software vulnerabilities 



Note : `The notebook simulates timing differences without actual real-world effects, but demonstrates core principles.`

# Side Channel Basics

## Remote Timing Attacks

# Timing Attack Simulation for Password Cracking
**By Sowrabh Adiga**  

## Introduction
This project simulates a timing-based side-channel attack to crack a password, implemented in [timing_attack_simulation.ipynb](https://github.com/sowrabh-adiga/cybersec/blob/main/timing_attack_simulation.ipynb), inspired by [video](https://youtu.be/V4E_0N_PvW8?si=66t5n3IhERz9fXCB) from the [youtube channel](https://www.youtube.com/@SideChannelSecurity) by Prof. Daniel Gruss

## Background
Timing attacks exploit execution time differences, as seen in Spectre etc, to leak data like passwords.

## Implementation
- **Environment**: Google collab, Jupyter, python 3.11.13
- **Steps**: Simulated a vulnerable password check, measured execution times over 40 trials. Then over 100,000 trials, using median/frequency analysis to guess digits for more accuracy.
- **Code**: [timing_attack_simulation.ipynb](https://github.com/sowrabh-adiga/cybersec/blob/main/timing_attack_simulation.ipynb)

## Results
- 40 trials, as per the video: Guessed “9870,” showing low-trial limitations.
- 100,000 trials: Guessed “9875” correctly, atleast over 7/10 iterations for each digit.
- Plot: Visualizes guess frequencies.

## Relevance
Inspired by Gruss’s lecture and CoreSec’s work on Spectre, CacheWarp (Kogler).

## Future Work
Explore cache-based attacks (e.g., prime+probe) and hardware defenses.

## Important details

The timing delay for sucessive bits can be used to find what bits of the key are valid.

The `unlock` function below simulates an online form that takes the key and checks element by element for a match. The password check fails when one of the elements doesn't match with password.
Why it's vulnerable: The time it takes to execute this function is directly proportional to the number of correct leading characters in the key.

I have assumed that there is no limit for how many password checks can be done for the  500,000 trials.

A time diffrence between each combination is being implemented by `time` python library:
```python
startTime = time.perf_counter()
unlock(testKey)
endTime = time.perf_counter()
```
Record time delay for all number (0-9) for multiple iterations. Multiple iterations are required because individual measurements risk data skew by outliers.

During initial runs it was observed that the mean was easliy being influenced by the outliers. So, I have used median in addition to running the simulations ten times and seeing which number repeats multiple times to determine the most likely key element

### Implementing logic to identify first key bit
the `run_single_simulation` function tries to measure the execution time of the `unlock` function for each attempt. We repeat it multiple times for each digit and store the median execution time.
The digit with max value is the result. To refine this further, we can introduce code that runs multiple iterations of the above logic and stores the estimates in a list. From this list, we can see which number is most likely to be the key digit simply by seeing how many times that number was  repeatedly estimated. (The more count, the better the estimate) 
```output
--- Final Results ---
All guesses from the best--experiment: ['5', '7', '9', '9', '0', '9', '9', '0', '9', '5']
Frequency of guesses: Counter({'9': 5, '5': 2, '0': 2, '7': 1})

The most frequent guess across all 10 runs is: '9'
The actual first digit of the password is: '9'
```
![](https://github.com/sowrabh-adiga/cybersec/blob/main/files/download%20(2).png)

### Implementing for whole 4 bits of key 
The code remains same for most part except for last bit. The last bit exits immediately after the match so has very less delay. so only the last bit has to have min delay values in the median delay list.
```python
if len(prefix) < passwordLen -1:
    estimatedKey = max(medianTimes, key=medianTimes.get)
else:
    estimatedKey = min(medianTimes, key=medianTimes.get)
    allGuesses.append(estimatedKey)
```

output looks like this:
see full output in cell 4 in [timing_attack_simulation.ipynb](https://github.com/sowrabh-adiga/cybersec/blob/main/timing_attack_simulation.ipynb)
```output
<clipped>
.
.
.
[5, 5, 0, 5, 0, 9, 0, 5, 5, 5]
Counter({5: 6, 0: 3, 9: 1})
Most likely digit is: '5'
Password so far: '9875'

--- ATTACK SUCCESSFUL ---
The guessed password is: '9875'
The actual password is: '9875'
```



