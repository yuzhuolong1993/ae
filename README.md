# net-lock
## 0. Introduction<br>
- A prototype implementation for <NetLock: Fast, Centralized Lock Management Using Programmable Switches>

## 1. Environment requirement<br>
- Barefoot Tofino switch.<br>
  > blabla<br>
- Servers with dpdk-compatible NIC.<br>
  > blabla<br>

## 2. Content<br>
   - dpdk_code: C code to run on lock servers and client<br>
   - switch_code: p4 code to run on Programmable switch<br>
   - results: <br>
   - logs: <br>
   - console.py: A script to help run different set of evaluations<br>
   - README.md: This file<br>

## 3. How to run<br>
- Configure the parameters based on your environment
  - config.py: provide the information of your servers (username, passwd, hostname, dir)<br>
  - client.c: <br>
- Configure the switch
  - Setup p4 running environment<br>
  - Copy the files to the switch: python console.py init_sync_switch<br>
  - Compile the netlock: python console.py compile_switch<br>
- Configure the servers
  - Setup dpdk environment<br>
  - Copy the files to the server: python console.py init_sync_server<br>
  - Compile the clients and lock servers: python console.py compile_host<br>
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
  - Figure 15: `python console.py failover`
