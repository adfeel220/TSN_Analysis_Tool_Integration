Saihu: A Common Interface for Worst-Case Delay Analysis of Time-Sensitive Networks
=======================
Author: Chun-Tso Tsai
Advisors: Seyed Mohammadhossein Tabatabaee, Stéphan Plassart, Jean-Yves Le Boudec
Date: 2023-01-05
Institute: Computer Communications and Applications Laboratory 2 (LCA2), École Polytechnique Fédérale de Lausane (EPFL)

Table of Contents
========================
* [Introduction](#introduction)
    * [Credit](#credit)
* [Project Structure](#project-structure)
    * [File Description](#file-description)
    * [Credits to Files](#credits-to-files)
* [Installation](#installation)
    * [Requirements](#requirements)
    * [Dependencies](#dependency)
* [How to Use This Tool](#how-to-use)
    * [Network Description File](#network-description-file)
        * [Network Definition](#network-definition)
        * [Physical Network](#physical-network)
        * [Output-Port Network](#output-port-network)
    * [Analysis Tools](#analysis-tools)
        * [Tool Specification](#tool-specification)
        * [Public Methods](#public-methods)
    * [Network Generation](#network-generation)
* [Example](#example)
    * [Specific Tool](#specific-tool)
    * [Selecting Shaper](#selecting-shaper)
    * [Specific Networks](#specific-networks)
    * [Generate Network and Analysis](#generate-network-and-analysis)
* [Extend this Project](#extend-this-project)
    * [Files](#files)
    * [Standard Analysis Result](#standard-analysis-result)
* [Contact](#contact)


# Introduction
**Time-Sensitive Networking (TSN)** analysis focuses on giving deterministic delay or backlog guarantees. This project integrates 4 different TSN analysis tools, including `Linear TFA Solver`, [NetCal/DNC](https://github.com/NetCal/DNC), [xTFA](https://gitlab.isae-supaero.fr/l.thomas/xtfa), and [panco](https://github.com/Huawei-Paris-Research-Center/panco). The users can use a common interface to compute network delay bounds obtained by different tools, and write the results into a formated report. All these actions requires only a few lines of code.

The name **SAIHU** comes from **S**uperimposed worst-case delay
**A**nalysis **I**nterface for **H**uman-friendly **U**sage." It’s also
inspired by the Taiwanese word ‘師傅 (sai-hū)’, which means a master, an expert, or a qualified worker.

## Credit
Here are the authors that implemented the individual tools used in this project.
- `Linear TFA Solver`: I implemented it myself following the TFA algorithm used in [Trade-off between accuracy and tractability of Network Calculus in FIFO networks](https://doi.org/10.1016/j.peva.2021.102250).
- `xTFA`: This tool is implemented in `Python` by [Ludovic Thomas](https://people.epfl.ch/ludovic.thomas/?lang=en).
- `NetCal/DNC`: This tool is implemented in `JAVA` by the NetCal team. You can visit [their repository](https://github.com/NetCal/DNC) and here are the academic references:
    - Arbitrary Multiplexing:
    ```
    @inproceedings{DiscoDNCv2,
        author    = {Steffen Bondorf and Jens B. Schmitt},
        title     = {The {DiscoDNC} v2 -- A Comprehensive Tool for Deterministic Network Calculus},
        booktitle = {Proc. of the International Conference on Performance Evaluation Methodologies and Tools},
        series    = {ValueTools '14},
        pages     = {44--49},
        month     = {December},
        year      = 2014,
        url       = {https://dl.acm.org/citation.cfm?id=2747659}
    }
    ```
    - FIFO Multiplexing:
    ```
    @inproceedings{LUDBFF,
        author    = {Alexander Scheffler and Steffen Bondorf},
        title     = {Network Calculus for Bounding Delays in Feedforward Networks of {FIFO} Queueing Systems},
        booktitle = {Proc. of the 18th International Conference on Quantitative Evaluation of Systems},
        series    = {QEST '21},
        pages     = {149--167},
        month     = {August},
        year      = 2021,
        url       = {https://link.springer.com/chapter/10.1007/978-3-030-85172-9_8}
    }
    ```
- `panco`: The tool is implemented in `Python` by [Anne Bouillard](https://ieeexplore.ieee.org/author/38526153500) at Huawei Paris Research Center. Here is the [original repository](https://github.com/Huawei-Paris-Research-Center/panco). The following is the academic reference:
    ```
    @article{BOUILLARD2022102250,
        title    = {Trade-off between accuracy and tractability of Network Calculus in FIFO networks},
        journal  = {Performance Evaluation},
        volume   = {153},
        pages    = {102250},
        year     = {2022},
        issn     = {0166-5316},
        doi      = {https://doi.org/10.1016/j.peva.2021.102250},
        url      = {https://www.sciencedirect.com/science/article/pii/S0166531621000675},
        author   = {Anne Bouillard},
        keywords = {Network Calculus, FIFO systems, Linear programming},
        abstract = {Computing accurate deterministic performance bounds is a strong need for communication technologies having stringent requirements on latency and reliability. Within new scheduling protocols such as TSN, the FIFO policy remains at work inside each class of communication. In this paper, we focus on computing deterministic performance bounds in FIFO networks in the Network Calculus framework. We propose a new algorithm based on linear programming that presents a trade-off between accuracy and tractability. This algorithm is first presented for tree networks. Next, we generalize our approach and present a linear program for computing performance bounds for arbitrary topologies, including cyclic dependencies. Finally, we provide numerical results, both of toy examples and realistic topologies, to assess the interest of our approach.}
    }
    ```


# Project Structure
```
.
└- interface.py
└- result.py
└- environment.yml
└- README.md
└- example
│  └- example.py
│  └- demo.json
│  └- demo.xml
│  └- demo_report.md
│  └- demo_data.json
│  └- temp
│     └- ... (execution artifacts)
│
└- src
   └- javapy
   │  └- dnc_analysis.jar
   │  └- dnc_exe.py
   │  └- NetworkAnalysis
   │     └- NetArgParser.java
   │     └- NetworkScriptHandler.java
   │     └- NetworkAnalysis.java
   │
   └- Linear_TFA
   │  └- Linear_TFA.py
   │  └- ...
   │
   └- xTFA
   │  └- ...
   │
   └- panco
   │  └- lp_solve
   │  └- lpSolvePath.py
   │  └- panco_analyzer.py
   │  └- ...
   └- netscript
      └- netdef.py
      └- netscript.py
      └- net_gen.py
      └- unit_util.py
```
## File description
- `interface.py`: The general interface to use the analysis tools. Generally speaking, user can only import function from here to access all functionalities.
- `result.py`: The formated result class from all tools.
- `example/`:
    - `example.py`: Example on how to use it.
    - `demo.json`: A demo network definition file in output-port json format. (more detail in [Output Port Network](#output-port-network))
    - `demo.xml`: A demo network definition file in physical network XML format. (more detail in [Physical Network](#physical-network))
    - `demo_report.md`: An example of formatted human-readable report.
    - `demo_data.json`: An example of formatted `json` listing results.
- `src/`:
    - `javapy/`:
        - `dnc_analysis.jar`: The custom `.jar` file that can execute DNC analysis. Note that if you build a new `dnc_analysis.jar`, sometimes you may need the following command to make it executable.
            ```
            zip -d dnc_analysis.jar 'META-INF/*.SF' 'META-INF/*.RSA' 'META-INF/*.DSA'
            ```
        - `dnc_exe.py`: The `Python` implementation on executing `dnc_analysis.jar` and capture the results.
        - `NetworkAnalysis`: A folder that represents a java package of my implementation on the interaction between my interface and DNC tool.
            - `NetArgParser.java`: Parsing the input arguments.
            - `NetworkScriptHandler.java`: Construct a DNC `ServerGraph` object for further analysis based on the input network description file.
            - `NetworkAnalysis`: Perform analysis given the tools specified in input arguments and print them in `.json` format.
    - `Linear_TFA/Linear_TFA.py`: The implementation of `Linear TFA solver`.
    - `xTFA/`: The original `xTFA` module from [xTFA](https://gitlab.isae-supaero.fr/l.thomas/xtfa)
    - `panco/`:
        - `...`: The original `panco` module from [panco](https://github.com/Huawei-Paris-Research-Center/panco)
        - `lp_solve`: The `lp_solve` executable downloaded and installed from [lpsolve](https://sourceforge.net/projects/lpsolve/)
        - `lpSolvePath.py`: You need to change `LPSOLVEPATH` in this file if you place your lpsolve executable at a different directory.
        - `panco_analyzer.py`: To load and build networks, and performing analysis using `panco` tools.
    - `netscript/`:
        - `netdef.py`: The `Python` representation of physical network in _WOPANet_ `.xml` format and output-port abstraction of `.json` format.
        - `netscript.py`: The sciprt handler to manipulate network definition files.
        - `net_gen.py`: Methods to automatically generate interleave/ring/mesh network with arbitrary number of homogeneous servers.
        - `unit_util.py`: Methods to do multiplier/unit parsing and manipulation.
- `README.md`: This `README`

## Credits to Files
- `NetCal/DNC`: The file `dnc_analysis.jar` is built based on the project [DNC](https://github.com/NetCal/DNC). I only implemented `NetworkAnalysis` package to allow analysing with `DNC` while using my network description file as input and my standard result format as output. More you can find more details about my input/output at [network description file](#network-description-file) and [standard result](#standard-analysis-result).
- `xTFA`: All files under the folder `xtfa` are implemented by [Ludovic Thomas](https://people.epfl.ch/ludovic.thomas/?lang=en) on this [repository](https://gitlab.isae-supaero.fr/l.thomas/xtfa).
- `panco`: Under the folder `panco`, I only implemented `panco_analyzer.py` to bridge my input/output format to panco tools. All files other than `panco_analyzer.py` and `lp_solve` are credited to [Anne Bouillard](https://ieeexplore.ieee.org/author/38526153500) with the complete project at [this repository](https://github.com/Huawei-Paris-Research-Center/panco)

# Installation
Please install the following requirements on your machine.
## Requirements
- `lpsolve`: Download and installed from [lpsolve](https://sourceforge.net/projects/lpsolve/). The `lp_solve` in the project is built on `macOS 12.6`, you may need to build a different version on your machine.
- `Java`: `JDK 16.0.2`
- `Python`: Create an environment using `environment.yml` or installing `numpy`, `networkx`, `matplotlib`, `pulp`, and `mdutils` with `Python>=3.8`.

## Dependencies
You may also choose to not install the environment if you choose not to use all tools included in this module. Here are the list of dependency to each tool, you may refer to this list to decide which environment setting you need.
- `Saihu`: This is a **MUST-HAVE** to use the interface. Requires `Python>=3.8`/`numpy`/`networkx`/`matplotlib`/`mdutils`
- `panco`: Requires `Python`/`lpsolve`/`panco package`
- `Linear TFA`: Requires `Python`/`pulp`
- `xTFA`: Requires `Python`/`xtfa package`
- `DNC`: Requires `Java` with `JDK 16`

# How to Use
You need to write your network in one of the network description format specified below. Then use the Python interface to do the analysis.
You would also be able to generate a network by the built-in network generating functions specified in [Network Generation](#network-generation).
## Network Description File
The tool accepts 2 formats of network description files. The first is **Physical Network** and the other is **Output Port Network**. Please find the detail below.

### Network Definition
In this section, we briefly introduce the relation between the 2 formats.

Take the following network as an example
![Demo network](images/demo_net.png)
This network consists of 
- 3 flows: `f0`, `f1` and `f2`. Their paths are labelled with blue, red and green respectively. 
- 2 switches: `s0` and `s1` with `i[x]` and `o[x]` being their input/output ports.
- 5 end-stations: `src0`, `src1`, `src2`, `sink0` and `sink1`.
- 6 links: Labelled as grey cylinders.

Each source of flow (`src[x]`) provides data flow modelled as an arrival curve composed of token-bucket curves; while each output port provides service to process these data with a service curve composed of rate-latency curves.
A **Physical Network** aims at directly describing the above network

On the other hand, since the delay are mostly caused by data flows competing the resource of output ports, we can directly model the network as output ports as follows
<p style="text-align:center;"><img src="images/op_net.png" alt="output-port-network" width="300"/></p>

Here we remove most details and only focus on the output ports. The 3 flows provides data modelled by an arrival curve, and the 3 involved output ports process them with under the model of their service curves. This abstraction still captures the essential of the network, one can derive the same delay bound at each output port as the completed physical network.
We refer this abstracted network as an **Output Port Network**.
### Physical Network
In this section, we introduce the format to define a physical network.

A physical network is defined in `WOPANet` format as a `.xml` file. It contains only one `elements` with the following attributes:
- `network`: Has the following attributes
    - `name`: Name of the network
    - `technology`: Global specifications connected by _"+"_, where you can use
        1. `FIFO`: FIFO property
        2. `IS`: Input shaper
        3. `PK`: Packetizer
        4. `CEIL`: Fixed precision, can reduce computation time but slightly reduce precision.
        5. `MOH` and `TDMI`: Can improve delay bounds. For technical details please contact the author of `xTFA` [Ludovic Thomas](mailto:ludovic.thomas@epfl.ch).
    - Other attributes are optional, but used as a global parameter. If some parameters are not specified in each server/flow, the system uses the global parameter defined here instead.

    Example:
    ```
	<network name="example-network" technology="FIFO+IS+CEIL" overhead="0" maximum-packet-size="0"/>
    ```
- `station`/`switch`: The two names would not affect the analysis result, but for representing the physical network and readability. Can have the following attributes
    - `name`: Name of station/switch.
    - `service-latency`: The latency of the rate-latency curve. Can assign different time units, `s`/`ms`/`us`, etc.
    - `service-rate`: The service rate of the rate-latency curve. Can assign different rate units, `Mbps`... (But not `bps` alone)

    Example:
    ```
    <station service-latency="1us" service-rate="100Gbps" name="src0"/>
	<switch  service-latency="1us" service-rate="100Gbps" name="s0"/>
    ```
- `link`: Connection link between ports, has the following attributes
    - `name`: Name of link.
    - `from`: Which station/switch the link is connected from. Need to be name of station/switch.
    - `to`: Which station/switch the link is connected to. Need to be name of station/switch.
    - `fromPort`: The port number used for incoming station/switch.
    - `toPort`: The port number used for outgoing station/switch.
    - `transmission-capacity`: The transmission capacity of the link. Can assign different rate units, `Mbps`... (But not `bps` alone)

    Example:
    ```
	<link from="src0" to="s0" fromPort="o0" toPort="i0" transmission-capacity="200Gbps" name="src_sw_0"/>
    ```
    Note: Service curve can also be defined on links as defined on station/switch. In this way, the service curve defined on the link that directly attached to the output port would be considered first.
    Note 2: Transmission capacity can also be defined on station/switch, that would be the default capacity to all links attached to the station/switch if on capacity is defined on that link.
- `flow`: Flow of network, has attributes:
    - `name`: Name of flow
    - `arrival-curve`: Type of arrival curve, `leaky-bucket` for example. The curve can also be periodic but currently tools other than `xTFA` can not process it.
    - `lb-burst`: burst of leaky-bucket curve. Can assign different data units, `B` for Byte or `b` for bit, defulat is `B`.
    - `lb-rate`: arrival rate of leady-bucket curve. Can assign different rate units, `Mbps`... (But not `bps` alone)
    - `source`: The source of this flow, must be a station or switch.
    
    A `flow` element can have multiple paths, it is then treated as separated flows with the same arrival parameters.
    - `target`: Each target is a path, can assign a `name` to it. A list of nodes is written as its sub-element-tree. Each node is specified by a `path` entry with attribute `node` equals to a station/switch.

    Example:
    ```
    <flow name="f0" arrival-curve="leaky-bucket" lb-burst="1b" lb-rate="1" source="st-0">
        <target name="p1">
            <path node="sw0"/>
            <path node="sw1"/>
            <path node="sw2"/>
            <path node="sw3"/>
	    </target>
        <target name="p2">
            <path node="sw0"/>
            <path node="sw3"/>
            <path node="sw5"/>
            <path node="sw6"/>
        </target>
	</flow>
    ```

### Output-Port Network
An Output-port network is defined as a `.json` file. It contains only one `JSON Object` with the following attributes:
- `network`: General network information, for example
    ```
    "network": {
        "name": "my network",
        "multiplexing": "FIFO",    // or "ARBITRARY"
        "time_unit": "us",         // Default unit used in the following
        "data_unit": "MB",         // sections if no specific unit is 
        "rate_unit": "Gbps"        // defined along the numbers
    }
    ```
- `flows`: array of flows, each flow has the following attributes
    - `name`: Name of flow
    - `path`: An array to represent path of flow, written as names of servers defined in `servers`. 
    - `arrival_curve`: A multi-segment curve, which has 2 attributes `bursts` and `rates`. Both attributes are arrays and must have the same length. Burst and arrival rate wit hthe same index correspond to a token-bucket curve, and the final arrival curve is defined as minimum of all these curves.
    - `max/min_packet_length`: The maximum/minimum packet length of the flow.

    Example:
    ```
    "flows": [
        {
            "name": "f0",
            "path": ["s0-o0", "s1-o0],   // output port names
            "arrival_curve": {
                "bursts": [1, "5GB"],    // 1. Multiple segments
                "rates": [10, "100Gbps"] // 2. Accept number with units or pure number (default unit will be applied)
            },
            "max_packet_length": 20,
            "min_packet_length": "1B",
            "data_unit": "kB"
        },
        ...
    ]
    ```
- `servers`: an array of servers, each server has the following attributes
    - `name`: Name of server
    - `service_curve`: A multi-segment curve, which has 2 attributes `latencies` and `rates`. Both attributes must have the same length. Latency and service rate with the same index correspond to a rate-latency curve. The final service curve is the maximum of all these curves.
    - `capacity`: The output capacity of the server.

    Example:
    ```
    "servers": [
        {
            "name": "s0-o0",
            "service_curve": {
                "latencies": [1e-2, "10ms"],
                "rates": ["2Gbps", "30Gbps"]
            },
            "capacity": 100,
            "rate_unit": "Gbps"
        },
        ...
    ]
    ```

## Analysis Tools
### Tool Specification
- Available method for tools
    | Method\Tool | DNC | xTFA | Panco | Linear TFA |
    | :--: | :-: | :--: | :---: | :----: |
    | **TFA** | V | V | V | V |
    | **SFA** | V |   | V |   |
    | **PLP** |   |   | V |   |
    | **ELP** |   |   | V |   |
    | **PMOO** | V |
    | **TMA** | V |
    Note: TMA stands for Tandem Matching Analysis
- Constraints
    | Spec\Tool | DNC | xTFA | Panco | Linear TFA |
    | :--: | :-: | :--: | :---: | :----: |
    | **Manual Tune Shaping*** |  | V | V | V |
    | **Cyclic Dependent Network***   |  | V | V | V |

    Note: 1. `DNC` cannot set output shaping together with _FIFO_ multiplexing, unless you are using _Arbitrary_ multiplexing (for PMOO or TMA). 2. `Panco-ELP` doesn't allow cyclic dependent network.

### Public Methods
To use our general interface, you need to first import class `TSN_Analyzer` from the file `src/interface.py`.
```
from interface import TSN_Analyzer
```
Here is a list of all available methods
* [init](#init)
* [set_shaping_mode](#set_shaping_mode)
* [convert_netfile](#convert_netfile)
* [analyze_all](#analyze_all)
* [analyze_xtfa](#analyze_xtfa)
* [analyze_linear](#analyze_linear)
* [analyze_panco](#analyze_panco)
* [analyze_dnc](#analyze_dnc)
* [export](#export)
* [write_result_json](#write_result_json)
* [write_report_md](#write_report_md)
* [clear](#clear)

### Init
An analyzer can be initialized by
```
analyzer = TSN_Analyzer(netfile, jar_path, temp_path, shaping)
```
All arguments are optional, each of them represents
- `netfile`: The path to the network definition file, either a physical network or an output-port network.
- `jar_path`: The directory of the DNC `.jar` file. Default is in `javapy/`
- `temp_path`: The path to the tempary directory to put the execution artifacts.
- `output_shaping`: A string to select output shaping mode, can be _AUTO_, _ON_, or _OFF_. Default is _AUTO_, which means to consider output shaping if possible.

### set_shaping_mode
```
analyzer.set_shaping_mode(mode)
```
Set the output shaping usage of the analyzer by a string of either _AUTO_, _ON_, or _OFF_. _AUTO_ means considering output shaping if possible. _ON_ and _OFF_ are forcing analyzer to consider output shaping or not, and don't compute result if not possible.

### convert_netfile
```
outputport_net_file, physical_net_file = analyzer.convert_netfile(in_netfile, out_netfile, target)
```
Convert a network description file from either physical network or output-port network and return both paths of network definition files, where one is converted from the original infile.
- `in_netfile`: The path to input network description file in either physical network ending in `.xml` or output-port port network ending in `.json`.
- `out_netfile`: (Optional) The output path you want to store the converted file. If not assigned, it automatically generate a file in `temp_path` with name `tempnet`.
- `target`: (Optional) String of either `json` or `xml`. Return the target format only with the other output being `None` if target is specified and no conversion needed. Default is `None`, where it outputs both formats anyway.

The 2 return values are paths to the network description files, one is the output-port network and the other is the physical network.

### analyze_all
```
num_results = analyzer.analyze_all(netfile, methods, use_tfa, use_sfa)
```
Use all available tools to do analysis given the methods. Return number of results that are computed.
All parameters are **optional**:
- `netfile`: Executing using a specific network description file, use the one stored in the `analyzer.netfile` if it's `None`.
- `methods`: A list of strings or a string specifying the analysis method. For the available values please refer to [Tool Specification](#tool-specification).
- `use_tfa`/`use_sfa`: Boolean variables to select whether to use TFA/SFA bounds for improving PLP bounds. Only relevant when using `panco`. Default is both `True`.

The function returns the number of results loaded from the process.

### analyze_xtfa
```
analyzer.analyze_xtfa(netfile, methods)
```
Analyze the network with `xTFA`. All parameters are optional:
- `netfile`: Executing using a specific network description file, conversion is done if needed. Use the network stored in the `analyzer.netfile` if it's `None`.
- `methods`: A list of strings or a string specifying the analysis method. For the available values please refer to [Tool Specification](#tool-specification).

### analyze_linear
```
analyzer.analyze_linear(netfile, methods)
```
Analyze the network with `Linear TFA solver`. All parameters are optional:
- `netfile`: Executing using a specific network description file, conversion is done if needed. Use the network stored in the `analyzer.netfile` if it's `None`.
- `methods`: A list of strings or a string specifying the analysis method. For the available values please refer to [Tool Specification](#tool-specification).

### analyze_panco
```
analyzer.analyze_panco(netfile, methods, )
```
Analyze the network with `panco`. All parameters are optional:
- `netfile`: Executing using a specific network description file, use the one stored in the `analyzer.netfile` if it's `None`.
- `methods`: A list of strings or a string specifying the analysis method. For the available values please refer to [Tool Specification](#tool-specification).
- `use_tfa`/`use_sfa`: Boolean variables to select whether to use TFA/SFA bounds for improving PLP bounds. Only relevant when using PLP. Default is both `True`.

### analyze_dnc
```
analyzer.analyze_panco(netfile, methods, )
```
Analyze the network with `DNC`. All parameters are optional:
- `netfile`: Executing using a specific network description file, use the one stored in the `analyzer.netfile` if it's `None`.
- `methods`: A list of strings or a string specifying the analysis method. For the available values please refer to [Tool Specification](#tool-specification).

### export
```
analyzer.export(default_name, json_output, report_file)
```
Write the JSON result and Markdown report at the same time. All parameters are optional:
- `default_name`: The default file name stem if either `json_output` or `report_file` are not assigned or `None`. For example,
    ```
    analyzer.export("test")
    ```
    writes 2 files: `test_data.json` and `test_report.md`.
- `json_output`: The file name of the JSON output
- `report_file`: The file name of the Markdown report

### write_result_json
```
analyzer.write_result_json(output_file, clear)
```
Write the analyze result report from all the stored results.
- `output_file`: File name of the analysis result in JSON.
- `clear`: (Optional) Boolean deciding whether to clear the analyzer after finishing writing. Default is `True`.

### write_report_md
```
analyzer.write_report_md(output_file, clear)
```
Write the analyze result report from all the stored results.
- `output_file`: File name of the report to be generated
- `clear`: (Optional) Boolean deciding whether to clear the analyzer after finishing writing. Default is `True`.


### clear
Reset the analyzer.
```
analyzer.clear()
```

## Network Generation
Use functions from `src/netscript/net_gen.py`, there are 3 types of network that can be generated automatically. All servers and flows would have the same arrival curve, service curve, and shaper. Networks will be generated into output-port network in `.json` format.
- `generate_interleave_tandem()`
    The network with a chain topology. i.e. **s0** -> **s1** -> ... -> **sn-1**. 1 flow goes through all servers, and $n-1$ flows go from $k \rightarrow k+1$ for $k \in [0, n-2]$
- `generate_ring()`
    The network with a ring topology. i.e. **s0** -> **s1** -> ... -> **sn-1** -> **s0**. $n$ flows go from $k \rightarrow k-1\ mod\ n$
- `generate_mesh()`
    The network with a mesh topology. i.e.
    ```
    ------   ------      --------
    | s0 | - | s2 | ---- | sn-2 |
    ------   ------      --------   ------
           X        X ...         > | sn |
    ------   ------      --------   ------
    | s1 | - | s3 | ---- | sn-1 |
    ------   ------      --------
    ```
    Flows are all $2^{n/2}$ possible combinations from **s0** or **s1** to **sn**.

All 3 methods take the same parameters:
- `size`: The number of servers in the network.
- `burst`: The burst of arrival curve of each flow.
- `arr_rate`: The arrival rate of arrival curve of each flow.
- `pkt_leng`: Packet length of each flow.
- `latency`: Latency of each server.
- `ser_rate`: Service rate of each server.
- `capacity`: The transmission capacity of each server.
- `dir`: (Optional) The output file to store the generated network. Default is `None`, which is not writing the network to file, but return a dictionary of all information instead (the dictionary as loaded from a `.json` file.)

# Example
You may check [example.py](./example/example.py) for the simple example. Here I present the basic usage.
```
import sys
import os.path
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), "../src/")))

from interface import TSN_Analyzer

if __name__ == "__main__":
    analyzer = TSN_Analyzer("./demo.json", temp_path="./temp/", use_shaper="AUTO")
    analyzer.analyze_all()
    analyzer.export("test")
```

## Specific Tools
While `analyze_all` tries all possible tools, you may also specify which tool you want to use. For example,
```
analyzer = TSN_Analyzer("./demo.json")
analyzer.analyze_panco(methods=["TFA", "PLP"])
analyzer.analyze_dnc(methods="TFA")
analyzer.export("panco_dnc")     # Containing both panco and DNC results
```
Note that any function called `analyze_xxx` only puts the result into the analyzer's internal buffer. When you call `export`/`write_result_json`/`write_report_md`, it simply takes all the stored results and write them into the report. Under default setting, the buffer is cleaned after writing results.

You can also choose to not clearing the result buffer after writing a report.
```
analyzer = TSN_Analyzer("./demo.json")
analyzer.analyze_panco(methods=["TFA", "PLP"])
analyzer.export("panco", clear=False) # Write panco results

analyzer.analyze_dnc(methods="TFA")
analyzer.export("panco_dnc") # Write results including both panco and DNC
```


## Selecting Shaper
You may change shaper selection any time you want.
```
analyzer = TSN_Analyzer("./demo.json", use_shaper="ON")
analyzer.analyze_panco(methods=["TFA", "PLP"]) # Analysis with shaper
analyzer.set_shaper_usage("OFF")               # Turn off using shaper
analyzer.analyze_xtfa(methods="TFA")           # Analysis without shaper
analyzer.write_report_md("./demo_report.md")      # panco TFA & PLP is with shaper, xtfa is without shaper
```

## Specific Networks
### Late assignment of network
You may assign different networks every time you want to analyze.
```
analyzer = TSN_Analyzer()
analyzer.analyze_linear(netfile="./demo.json")
analyzer.write_report_md("./linear_report.md")
```
### Multiple networks
When there are multiple networks (different network names defined in `network` attributes of description files), the program generate multiple reports for each network. The extra report files are named by adding index to the original file. For example,
```
analyzer = TSN_Analyzer("./demo.xml", temp_path="./temp/", use_shaper="AUTO")
analyzer.analyze_all(methods=["TFA", "PLP"]) # Analyze "demo.xml"
analyzer.analyze_linear("./mesh.json")       # Analyze "mesh.json"
analyzer.write_report_md("./report.md")
```
Such code generates 2 files `report.md` and `report-1.md`, one reports `demo` and the other reports `mesh`.
However, I suggest users to manually write each result when needed because the suitable multiplier is chosen among all results.

### Incorrect type of network
The interface automatically converts the network description file when the input file is not in the right format for the tool. The desired input format for `xTFA` is physical network and all other tools take output-port network.
```
analyzer = TSN_Analyzer("./demo.xml", temp_path="./temp/", use_shaper="AUTO")
analyzer.analyze_linear()
analyzer.write_report_md("./report.md")
```
The above analysis still gives the report although `linear TFA solver` should take a output-port network in `.json` format.

## Generate Network and Analysis
You can use the network generation functions to generate the following 3 types of networks `interleave tandem`, `ring`, and `mesh`. About their topology and how the flows are assigned, please see [Network Generation](#network-generation).
Say you would like to generate a ring network of 10 servers, you can do the following.
```
from interface import TSN_Analyzer
from netscript.net_gen import *

generate_ring(size=10,
              burst=1,
              arr_rate=1,
              pkt_leng=0,
              latency=1,
              ser_rate=20,
              capacity=20,
              dir="./ring.json")
analyzer = TSN_Analyzer("./ring.json", temp_path="./temp/", use_shaper="AUTO")
analyzer.analyze_all()
analyzer.write_report_md("./ring_report.md")
```
The arguments for each type of network are the same, you can simply change `ring` to other generating functions.

# Extend this Project
This project is possible to extend for more functionality and possibly includes more TSN analysis tools. Here explains how can anyone extend the scope of this project.

## Files
Here are some files you may use/edit to allow the tool to fit your new tool.
- `interface.py`: You can add a new method in class `TSN_Analyzer` that takes the result from your new tool into a `TSN_result` class (see [here](#standard-analysis-result) for more details). This new method should be able to take 2 arguments:
    1. `netfile`: a path that specifies a network description file, it should use `self.netfile` if it's not specified. Depending on what type of network you need, you can use the private function `_arg_check` in `TSN_Analyzer` to obtain the desired input for analysis.
    2. `methods`: a list that can take method names as strings. Currently only support `TFA`, `SFA`, `PLP`, but feel free to add more since tools automatically ignores unavailable methods.

Depends on how your tool defines a network, you may need to implement the interface to take one of the [network description file format](#network-description-file), and load the analysis result into a `TSN_result` class.

- `netscript/netdef.py`: This file contains the methods to load and check the information defined in `xml` or `json` format network description files. If you decide to change the syntax required for the network description files, you may need to change the codes here.

- `netscript/netscript.py`: This file provides some methods to manipulate network description files including conversion between 2 formats; determine if a loaded network is cyclic; and get general network info.

- `netscript/unit_util.py`: This file provides utilities to parse and manipulate string of values with multipliers and units. For example, `10ms` represents 10 milliseconds and `100Gbps` represents 100 Gigabits-per-second. This file supports time, data, and data rate values.


## Standard Analysis Result
Please refer to the file `result.py`, any analysis result should be stored as a `TSN_result` class. So that you don't need to worry about how to write your result. The method `export`/`write_result_json`/`write_report_md` automatically processes `TSN_result` and write the corresponding output.
It's OK if your tool cannot fit all the properties of `TSN_result`, they all have default values, but you should at least specify `name`, `tool`, `method`, `graph`, and `flow_delays` to properly generate the report.

Here are the meaning of each property in `TSN_result`:
- `name`: Name of the network
- `tool`: Tool used in this analysis. e.g. `DNC` or `panco`
- `method`: Analysis method. e.g. `TFA` or `PLP`
- `graph`: The graph representation of the network, including unused links
- `server_delays`: Delays stored according to server names, unit in seconds. e.g. `{'s_1': 1.0, 's_2': 2.0}`
- `server_backlogs`: Delays stored according to server names, unit in bits. e.g. `{'s_1': 1, 's_2': 2}`
- `flow_paths`: Path of each flow as a list of servers according to flow names. e.g. `{'fl_1': ['s_1', 's_2']}`
- `flow_delays`: End-to-end delays of each flow. e.g. `{'fl_1': 4.0, 'fl_2': 7.0}`
- `exec_time`: Execution time of the analysis, unit in seconds
- `units`: The units used in this result
- `network_source`: The source file of the network definition
- `converted_from`: Which file it's converted from, it's an empty string if it's original

# Contact
Regarding any question/problem about this project, please contact me via E-mail: [academic](mailto:chun-tso.tsai@epfl.ch), [primary](mailto:tsai.chuntso@gmail.com), or [secondary](mailto:adfeel220@gmail.com).