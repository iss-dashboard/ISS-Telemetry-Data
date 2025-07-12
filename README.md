## About

This repository contains all the ISS's public telemetry data streamed by NASA's lightstreamer service since June 1, 2025. It gets updated automatically every minute by a bit of JavaScript code running on a Raspberry Pi Zero 2W (@branchybot). The data is stored as .csv files (comma seperated values); [a handy format](https://en.wikipedia.org/wiki/Comma-separated_values) that can be imported into Excel or read by a script in a programming language of your choice. On the first line of every file are its column headers, the first column is always a [UNIX timestamp](https://en.wikipedia.org/wiki/Unix_time).

The ISS Mimic project, and [their awesome site](https://iss-mimic.github.io/Mimic/) inspired me to create this repository. After all, if you can view the real-time information of the ISS, why not store it (and make it publicly accessible in the process)?

If this repository is useful to you, you might also be interested in another project of mine, a website that attempts to visualize (part of) the ISS data. Feel free to [take a look](https://iss-dashboard.github.io/).

**Disclaimer**: unfortunately, errors and power outages occur from time to time that put automatic updates to a temporary stop. I do my very best to prevent this :) It also happens quite frequently that NASA's server doesn't respond (but this is obviously out of my control). You can use the UNIX timestamp to deduce when there was downtime. If timestamps are missing, the Raspberry Pi was offline; if the timestamps are there but the data doesn't change at all, NASA is probably to blame.
## Example

To give an idea of how to approach the abundance of data, here's how you can use a Python script to generate a graph of the station's altitude over time. Make sure `matplotlib` is installed first:

```shell
pip install matplotlib
```

and `spacecraft_state/altitude.csv` is downloaded. Now run:

```python
import matplotlib.pyplot as plt

# make sure this variable points to the correct location
FILEPATH = 'path/to/altitude.csv'

# function to load lines from .csv file as tuples
def load_tuples(filename, types, sep=','):
    tuples = []
    err_msg = 'File not compatible with given types'
    with open(filename, 'r', encoding='utf-8') as file:
        for i, line in enumerate(file):
            if i == 0:  # skip csv headers
                continue
            fields = line.rstrip('\r\n').split(sep)
            assert len(fields) == len(types), err_msg
            entry = tuple(t(field) for field, t in zip(fields, types))
            tuples.append(entry)

    return tuples


data = load_tuples(FILEPATH, (int, float))

x, y = [], []

for entry in data:
    x.append(entry[0])
    y.append(entry[1])

fig, ax = plt.subplots()
ax.plot(x, y, linewidth=2.0)

ax.set(xlim=(1751700000, 1751718000), ylim=(413, 434))

plt.show()
```

You should have an image that looks something like this:

![](https://i.imgur.com/gGqgLuN.png)

Notice the periodicity. A next step could be to tweak the code could to reveal how much time it takes the ISS to complete its journey around the earth...
## Credit

By no means necessary (after all the data belongs to NASA), but always appreciated!
