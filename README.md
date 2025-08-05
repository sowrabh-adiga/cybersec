# cybersec
Collection of implementation of hardware and software vulnerabilities 


# Side Channel Basics

## Remote Timing Attacks

The timing delay for sucessive bits can be used to find what bits of the key are valid. [side_channel_.ipynb](https://github.com/sowrabh-adiga/cybersec/blob/main/side_channel_.ipynb) has the implementation of Remote Timing Attacks

The `unlock` function below simulates an online form that takes the key and checks element by element for a match. The password check fails when one of the elements doesn't match with password.
Why it's vulnerable: The time it takes to execute this function is directly proportional to the number of correct leading characters in the key.
```python
def unlock(key):
    if len(key) != len(password):
        return False

    for i in range(len(key)):
        if key[i] == password[i]:
            continue
        else:
            return False
    return True
```

Initally, I have assumed that there is no limit for how many password checks can be done.

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
![](https://github.com/sowrabh-adiga/cybersec/blob/main/files/download.png)

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



