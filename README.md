# net-lock
## 0. Introduction<br>
- A prototype implementation for paper <NetLock: Fast, Centralized Lock Management Using Programmable Switches>

## 1. Environment requirement<br>
- Hardware
  - A Barefoot Tofino switch.<br>
  - Servers with a DPDK-compatible NIC and multi-core CPU.<br>
- Software
  - Tofino SDK on the switch
  - DPDK on the servers
    - You can either refer to this page or use the tools.sh script in dpdk_code/
      ```shell
      cd dpdk_code
      ./tools.sh install_dpdk
      ```
  - Python2.7, Paramiko at your endhost<br>
    ```pip install paramiko```

## 2. Content<br>
   - dpdk_code/: C code to run on lock servers and client.<br>
   - switch_code/: p4 code to run on Programmable switch.<br>
   - results/: We collect results from all the servers and store them here.<br>
   - logs/: We collect logs from all the servers and store them here.<br>
   - traces/: Some traces we use for the experiments.<br>
     - TPCC trace link: [Click to download!](http://cs.jhu.edu/~zhuolong/resource/tpcc_traces.zip) 
   - console.py: A script to help run different set of evaluations.<br>
   - config.py: Some parameters to configure.<br>
   - parser.py: A script to parse the raw results.<br>
   - README.md: This file.<br>

## 3. How to run<br>
- Configure the parameters in the files based on your environment
  - config.py: provide the information of your servers (username, passwd, hostname, dir)<br>
- Setup the switch
  - Setup p4 running environment<br>
  - Copy the files to the switch: `python console.py init_sync_switch`<br>
  - Compile the netlock: `python console.py compile_switch`<br>
- Setup the servers
  - Setup dpdk environment<br>
  - Copy the files to the server: `python console.py init_sync_server`<br>
  - Compile the clients and lock servers: `python console.py compile_host`<br>
  - Bind NIC to DPDK: `python console.py setup_dpdk`<br>
- After both the switch and the servers are correctly configured, you can replay the results by running console.py.<br>
  - Figure 8(a): `python console.py micro_bm_s`<br>
  - Figure 8(b): `python console.py micro_bm_x`<br>
  - Figure 8(c)(d): `python console.py micro_bm_cont`<br>
  - Figure 9: `python console.py micro_bm_only_server`<br>
  - Figure 10: `python console.py run_tpcc`<br>
  - Figure 11: `python console.py run_tpcc_ms`<br>
  - Figure 12: `python console.py priority`<br>
  - Figure 13: `python console.py mem_man`<br>
  - Figure 14(a): `python console.py think_time`<br>
  - Figure 14(b): `python console.py mem_size`<br>
  - Figure 15: `python console.py failover`<br>
- Interprete the results.<br>
  - `console.py` will collect raw results from the servers and store them at `results/`.
  - `parser.py` can parse the results (tput, avg. latency, etc.)
