+++
authors = ["Arman Drismir"]
title = "Using an old PC to mine crypto 😳 😬"
description = "I use an old pc to mine crypto and hook the whole thing up to a  prometheus/grafana setup."
date = 2024-12-05
[taxonomies]
tags = ["Crypto", "Grafana", "Prometheus"]
+++

The PC I am using to run this webserver also happens to have a NVIDIA GTX 1060. I may as well see if I can mine crypto with it. My poor 1060 is not strong enough mine blocks, even on smaller coins, so I needed to find a mining pool for a coin that would not overwhelm my 3GB of VRAM. I found NEXA on f2pool which works perfectly!

Using [lolMiner](https://github.com/Lolliedieb/lolMiner-releases) and an [f2pool](https://www.f2pool.com/) account I can start mining!

![lolMiner terminal output](lolMiner.png)

I cannot hope to make actual money from mining NEXA with a 1060 but I can hope to setup some nice analytics with prometheus and grafana.

Our starting point will be the api that lolMiner hosts on any machine it runs on. It serves stats in JSON format like this:
```JSON
{
    "Software": "lolMiner 1.92",
    "Session": {
        "Startup": 1734487547,
        "Startup_String": "2024-12-18_02-05-47",
        "Uptime": 3710,
        "Last_Update": 1734491257
    },
    "Num_Workers": 1,
    "Workers": [
        {
            "Index": 0,
            "Name": "NVIDIA GeForce GTX 1060 3GB",
            "Power": 86.117999999999995,
            "CCLK": 1936,
            "MCLK": 3802,
            "Core_Temp": 65,
            "Juc_Temp": 73,
            "Mem_Temp": 0,
            "Fan_Speed": 38,
            "LHR_Unlock_Pct": 0,
            "Dual_Factor": 0,
            "PCIE_Address": "11:0"
        }
    ],
    "Num_Algorithms": 1,
    "Algorithms": [
        {
            "Algorithm": "NexaPoW",
            "Algorithm_Appendix": "",
            "Pool": "nexa.f2pool.com:3400",
            "User": "cryptobro23.default",
            "Worker": "",
            "Performance_Unit": "Mh\/s",
            "Performance_Factor": 1000000,
            "Total_Performance": 5.6549161744285845,
            "Total_Accepted": 68,
            "Total_Rejected": 0,
            "Total_Stales": 0,
            "Total_Errors": 0,
            "Worker_Performance": [
                5.65
            ],
            "Worker_Accepted": [
                68
            ],
            "Worker_Rejected": [
                0
            ],
            "Worker_Stales": [
                0
            ],
            "Worker_Errors": [
                0
            ]
        }
    ]
}
```

Unfortunately prometheus cannot use JSON as a data source so I wrote `export_metrics.py` to host another endpoint that serves the lolMiner JSON as a prometheus target.

Now we have everything we need. After a few hours of setting up docker compose I had a fully dockerized grafana setup with lolMiner. You can check out the progress of lolMiner live at: [coco-mine.drismir.ca](https://coconut-mine.drismir.ca/d/de78xri7hrugwf/coco-min?&kiosk)