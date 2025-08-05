# cybersec
Collection of implementation of hardware and software vulnerabilities 


# Side Channel Basics

## Remote Timing Attacks

The timing delay for sucessive bits can be used to find what bits of the key are valid. [side_channel_.ipynb](https://github.com/sowrabh-adiga/cybersec/blob/main/side_channel_.ipynb) has the implementation of Remote Timing Attacks

The `unlock` function below simulates an online form that takes the key and checks element by element for a match. The password check fails when one of the elements doesn't match with password.
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




