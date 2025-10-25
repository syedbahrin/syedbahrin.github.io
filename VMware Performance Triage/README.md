VMware performance triage is a structured, four-pillar approach to diagnosing and resolving performance bottlenecks in a virtualized environment. The goal is to quickly pinpoint the resource constraint (CPU, Memory, Disk, or Network) responsible for the degraded performance.

### The Four Pillars of VMware Performance Triage

The core of VMware performance analysis lies in examining four key resources for contention (when demand exceeds supply) and saturation (when usage is near 100%).
| Pillar | Key Metric to Watch | Actionable Threshold (General Guideline) | Primary Cause of Contention |
| :--- | :--- | :--- | :--- |
| **CPU** | **CPU Ready Time** ($\% \text{RDY}$) | $\ge 5\%$ (for a single $\text{VM}$) | Over-provisioning $\text{vCPUs}$, resulting in the $\text{VM}$ waiting for physical $\text{CPU}$ time. |
| **Memory** | **Memory Ballooning** ($\text{MCTLSZ}$) | $\ge 0 \text{ KB}$ (Any positive value) | Host is running out of physical $\text{RAM}$ and is reclaiming memory from the $\text{VM}$. |
| **Disk** | **Latency** (Average Latency) | $\ge 20 \text{ ms}$ (for application servers) | Overloaded storage array, high $\text{IOPS}$, or slow physical disks ($\text{HDDs}$). |
| **Network** | **Dropped Packets** | $\ge 0$ (Any non-zero count) | High network traffic, insufficient port group capacity, or bad physical $\text{NIC}$. |

## VMware Triage Process: A Step-by-Step Flow

Use this structured approach to move from the guest OS problem to the underlying host resource bottleneck.

### Step 1: Establish the Scope (The "Is it Real?" Check)

1.  **Isolate the Problem:** Is the performance issue affecting **one VM**, **multiple VMs on one host**, **all VMs in the cluster**, or **the whole environment**?
2.  **Verify Guest OS Performance:** Check the performance monitors *inside* the problematic VM (e.g., Windows Task Manager, Linux `top`).
    * What resource is the guest OS reporting as the bottleneck ($\text{CPU}$ utilization, $\text{RAM}$ usage, Disk queue length)?
    * If the **Guest OS is bottle-necked**, proceed to Step 2.
    * If the **Guest OS looks fine**, the issue is likely **Network Latency** or a problem external to the VM.
3.  **Check VMware Tools:** Ensure **VMware Tools** is installed and up-to-date. Without it, performance is degraded and metrics are inaccurate.

## Step 2: Check the Host-Level Metrics (vSphere Client / `esxtop`)

Use the **vSphere Client Monitor** tab or the command-line utility **`esxtop`** (run on the $\text{ESXi}$ host via $\text{SSH}$) to identify the resource contention.

### A. CPU Contention

* **Primary Metric: CPU Ready Time ($\% \text{RDY}$)**
    * **Goal:** Keep $\% \text{RDY}$ low. A single $\text{vCPU}$ showing $\ge 5\%$ for a sustained period indicates an issue.
    * **If High: Remedy:** **Reduce the number of vCPUs** assigned to the $\text{VM}$ ($\text{vCPU}$ count is often over-provisioned), or **migrate the VM** to a less-busy host/cluster.

### B. Memory Contention

* **Primary Metrics: Memory Ballooning ($\text{MCTLSZ}$) & Swapping ($\text{SWCUR}$)**
    * **Goal:** $\text{MCTLSZ}$ and $\text{SWCUR}$ should be **0**.
    * **Ballooning:** If the host is initiating ballooning, it means the host is out of physical $\text{RAM}$.
    * **Swapping:** If the host is swapping memory to disk, performance is severely degraded (disk is much slower than $\text{RAM}$).
    * **If High: Remedy:** **Reduce the configured vRAM** on less-critical $\text{VMs}$, or **add physical RAM** to the $\text{ESXi}$ host.

### C. Storage (Disk) Contention

* **Primary Metric: Average Latency** (Kernel Latency $\text{KAVG}$ + Device Latency $\text{DAVG}$)
    * **Goal:** Total Latency should be $< 10 \text{ ms}$ for general use, and $< 20 \text{ ms}$ for high-$\text{I/O}$ applications.
    * **If High: Remedy:**
        * Check for problematic items: large/old **snapshots**, Storage $\text{I/O}$ Control ($\text{SIOC}$) limits, or a single busy $\text{VM}$ dominating $\text{I/O}$.
        * Migrate the $\text{VM}$ to a less-utilized datastore, or work with your storage team to troubleshoot the underlying $\text{SAN/NAS}$.

### D. Network Contention

* **Primary Metric: Dropped Transmit/Receive Packets**
    * **Goal:** Should be **0**.
    * **If High: Remedy:**
        * Check host-level metrics for high network usage on the $\text{vSwitch/Port}$ Group.
        * Ensure the $\text{VM}$ is using the **VMXNET3** adapter (the modern, high-performance adapter) instead of $\text{E1000}$.
        * Check the physical switch for link saturation, errors, or duplex mismatches.

### Tools for Triage

| Tool | Usage | Purpose |
| :--- | :--- | :--- |
| **vSphere Client/Web Client** | **Monitor** $\rightarrow$ **Performance** $\rightarrow$ **Advanced Charts** | Provides historical and real-time data for all key metrics across VMs, Hosts, and Clusters. The most common starting point. |
| `esxtop` / `resxtop` | $\text{SSH}$ to $\text{ESXi}$ host and run command | Provides high-granularity, real-time metrics, essential for deep-dive $\text{CPU}$, $\text{Memory}$, and $\text{Disk}$ triage. |
| **vRealize Operations ($\text{vROps}$)** | Dedicated product dashboard | Provides proactive capacity planning, deep analytics, and automated alerting across the entire environment. |
