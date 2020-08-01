# NetLock: Fast, Centralized Lock Management Using Programmable Switches
## 0. Introduction<br>
NetLock is a new centralized lock manager that co-designs servers and network switches to achieve high performance without sacrificing flexibility in policy support. NetLock exploits the capability of emerging programmable switches to directly process lock requests in the switch data plane.

![10b](figures/architecture.jpg)

More details are available in our SIGCOMM'20 paper. [[Paper]](http://cs.jhu.edu/~zhuolong/papers/sigcomm20netlock.pdf)

Below we show how to configure the environment, how to run the system, and how to reproduce the results.

## 1. Environment requirement<br>
- Hardware
  - A Barefoot Tofino switch.<br>
  - Servers with a DPDK-compatible NIC (we used an Intel XL710 for 40GbE QSFP+) and multi-core CPU.<br>
- Software<br>
  The current version of NetLock is tested on:<br>
    - Tofino SDK (version after 8.2.2) on the switch<br>
    - DPDK (16.11.1) on the servers<br>
      You can either refer to the [official guige](https://doc.dpdk.org/guides/linux_gsg/quick_start.html) or use the tools.sh script in dpdk_code/
        ```shell
        cd dpdk_code
        ./tools.sh install_dpdk
        ```
  We provide easy-to-use scripts to run the experiments and to analyze the results. To use the scripts, we need: 
    - Python2.7, Paramiko at your endhost<br>
      ```pip install paramiko```

## 2. Content<br>
   - dpdk_code/: C code to run on lock servers and client.<br>
   - switch_code/: p4 code to run on Programmable switch.<br>
   - results/: We collect results from all the servers and store them here.<br>
   - logs/: We collect logs from all the servers and store them here.<br>
   - traces/: Some traces we use for the experiments.<br>
     - TPCC trace link: [Click to download!](http://cs.jhu.edu/~zhuolong/resource/tpcc_traces.zip)<br>
     - Microbenchmark trace link: [Click to download!](http://cs.jhu.edu/~zhuolong/resource/microbenchmark.zip)<br>
     - Move the zip to the corresponding folder and unzip.<br>
   - console.py: A script to help run different set of evaluations.<br>
   - config.py: Some parameters to configure.<br>
   - parser.py: A script to parse the raw results.<br>
   - README.md: This file.<br>

## 3. How to run<br>
First the traces should be downloaded to the traces/ directory.
```shell
cd traces
wget [The link is in the Content section]
unzip tpcc_traces.zip -d tpcc_traces
unzip microbenchmark.zip -d microbenchmark
```
Then you can either run manually execute programs on the switch and the servers, or use the script (console.py) we provided (recommended).
- To use scripts (Recommended)<br>
  - Configure the parameters in files based on your environment.<br>
    - config.py: provide the information of your servers (username, passwd, hostname, dir)<br>
    - switch_code/netlock/controller_init/ports.json: use the information (actual enabled ports) on your switch.
  - Environment setup<br>
    - Setup the switch<br>
      - Setup the necessary environment variables to point to the appropriate locations.<br>
      - Copy the files to the switch.<br>
        - `python console.py init_sync_switch`<br>
      - Compile the NetLock.<br>
        - `python console.py compile_switch`<br>
    - Setup the servers<br>
      - Setup DPDK environment (install dpdk, and set correct environment variables).<br>
      - Copy the files to the servers.<br>
        - `python console.py init_sync_server`<br>
      - Compile the clients and lock servers.<br>
        - `python console.py compile_host`<br>
        - It will compile for both lock servers and clients.<br>
      - Bind NIC to DPDK.<br>
        - `python console.py setup_dpdk`<br>
        - It will bind NIC to DPDK for both lock servers and clients.<br>
  - Run the programs<br>
    - Run NetLock on the switch<br>
      - `python console.py run_netlock`<br>
      - It will bring up both the data-plane module and the control-plane module.
    - Run lock servers<br>
      - `python console.py run_server`<br>
      - It will run the lock servers with parameters defined in the script.
    - Run clients<br>
      - `python console.py run_client`<br>
      - It will run the clients with parameters defined in the script.<br>
  - Get the results and logs<br>
    The results are located at results/, and the log files are located at logs/<br>
    - To easily analyze the results, you can grab results from all the clients/servers to the local machine where you are running all the commands.
      - `python console.py grab_result`
  - Kill the processes<br>
    - Kill the switch process
      - `python console.py kill_switch`
    - Kill the lock server and client processes
      - `python console.py kill_host`
    - Kill all the processes (switch, lock servers, clients)
      - `python console.py kill_all`
  - Other commands<br>
    There are also some other commands you can use:
    - `python console.py sync_switch`
      - copy the local "switch code" to the switch
    - `python console.py sync_host`
      - copy the local "client code" and "lock server code" to the servers
    - `python console.py sync_trace`
      - copy the traces to the servers
    - `python console.py clean_result`
      - clean up the results/ directory<br>
- To manually run (Not recommended)<br>
  - Configure the ports information<br>
    - switch_code/netlock/controller_init/ports.json: use the information (actual enabled ports) on your switch.
  - Environment setup<br>
    - Setup the switch<br>
      - Setup the necessary environment variables to point to the appropriate locations.<br>
      - Copy the files to the switch.<br>
      - Compile the NetLock.<br>
        ```shell
        cd switch_code/netlock/p4src
        python tool.py compile netlock.p4
        ```
    - Setup the servers<br>
      - Setup DPDK environment (install dpdk, and set correct environment variables).<br>
      - Copy the files to the servers.<br>
      - Bind NIC to DPDK.<br>
        ```shell
        cd dpdk_code
        ./tools.sh setup_dpdk
        ```
      - Compile the clients.<br>
        ```shell
        cd dpdk_code/client_code
        make
        ```
      - Compile the lock servers.<br>
        ```shell
        cd dpdk_code/lock_server_code
        make
        ```
  - Run the programs<br>
    - Run NetLock on the switch<br>
      ```shell
      cd switch_code/netlock/p4src
      python tool.py start_switch netlock 
      python tool.py ptf_test ../controller_init netlock (Execute in another window)
      ```
    - Run lock servers<br>
      - See [here](dpdk_code/README.md).<br>
    - Run clients<br>
      - See [here](dpdk_code/README.md).<br>
  - Results and logs
    The results are located at results/, and the log files are located at logs/<br>
## 4. How to reproduce the results<br>
- Copy the traces.<br>
  ```shell
  cd traces
  wget [The link is in the Content section]
  unzip tpcc_traces.zip -d tpcc_traces
  unzip microbenchmark.zip -d microbenchmark
  ```
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
  - Figure 13: `python console.py mem_man`<br>
  - Figure 14: `python console.py mem_size`<br>
- Interprete the results.<br>
  - `console.py` will collect raw results from the servers and store them at `results/`.
  - `parser.py` can parse the results (tput, avg. latency, etc.)
    - `parser.py` can help process the result files to get the throughput/latency.
    - It can process different metrics by running `python parser.py [metric] [task_name]`:
      - metric:
        - tput: lock throughput.
        - txn_tput: transaction throughput.
        - avg_latency/99_latency/99.9_latency: the average/99%/99.9% latency for locks.
        - txn_avg_latency/txn_99_latency/txn_99.9_latency: the average/99%/99.9% latency for transactions.
      - task_name:
        - micro_bm_s: microbenchmark - shared locks.
        - micro_bm_x: microbenchmark - exclusive locks w/o contention.
        - micro_bm_cont: microbenchmark - exclusive locks w/ contention.
        - tpcc: TPC-C workload with 10v2 setting.
        - tpcc_ms: TPC-C workload with 6v6 setting.
        - mem_man: memory management experiment.
        - mem_size: memory size experiment.
    - For example, after running `python console.py run_tpcc`, you can run:
      - `python parser.py txn_tput tpcc` will give you the transaction throughput. It will give the results we used for Figure 10(b) (Shown below).
      ![10b](figures/10b.jpg)

     
