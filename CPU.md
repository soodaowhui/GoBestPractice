# 不同操作系统中获取CPU使用率的方法

两种统计思路：

- 从ps命令获取CPU占用情况，缺点是此命令本身需要占用一定CPU资源
- 间隔一定时间，两次从proc文件获取CPU占用情况之后取差，缺点是两次时间间隔不能太短（一般1S以上比较稳妥），所以不能高频的使用


```
type Process struct {
	pid int
	cpu float64
}


// 分操作系统计算CPU使用率
func GetCPUPercent() float64 {
	if runtime.GOOS == "darwin"{
		return getCPUPercentByPS()
	}

	// else: linux
	return getCPUPercentByProc()
}

// 通用方案，mac, linux都可用，但是自身占用CPU严重，不适合线上服务使用，可以用于mac os
func getCPUPercentByPS() float64 {
	cmd := exec.Command("ps", "aux")
	var out bytes.Buffer
	cmd.Stdout = &out
	err := cmd.Run()
	if err != nil {
		panic(err)
	}
	processes := make([]*Process, 0)
	for {
		line, err := out.ReadString('\n')
		if err!=nil {
			break;
		}
		tokens := strings.Split(line, " ")
		ft := make([]string, 0)
		for _, t := range(tokens) {
			if t!="" && t!="\t" {
				ft = append(ft, t)
			}
		}
		pid, err := strconv.Atoi(ft[1])
		if err!=nil {
			continue
		}
		cpuRatio, err := strconv.ParseFloat(ft[2], 64)
		if err!=nil {
			panic(err)
		}
		processes = append(processes, &Process{pid, cpuRatio})
	}
	totalCPU := float64(0)
	for _, p := range(processes) {
		totalCPU+=p.cpu
	}
	return totalCPU
}

// linux系统方案
func getCPUPercentByProc() float64  {
	time_interval := 1 // this number represents seconds

	prevCPUStats := readCPUStats()
	time.Sleep(time.Second * time.Duration(time_interval))
	currCPUStats := readCPUStats()
	_,totalUsage := calcMyCPUStats(currCPUStats, prevCPUStats)
	return float64(totalUsage)
}

func readCPUStats() *linuxproc.Stat {
	stat, err := linuxproc.ReadStat("/proc/stat")
	if err != nil {
		log.Fatal("stat read fail")
	}
	// fmt.Println(stat)
	return stat
}

func calcMyCPUStats(curr, prev *linuxproc.Stat) (map[string]float32,float32) {
	stats := make(map[string]float32)
	totalUsage := float32(0)
	n := runtime.NumCPU()
	for i:=0;i<n;i++{
		cpuName := fmt.Sprint("CPU",i)
		usage := calcSingleCoreUsage(curr.CPUStats[i], prev.CPUStats[i])
		stats[cpuName] = usage
		totalUsage += usage

	}
	return stats,totalUsage
}

func calcSingleCoreUsage(curr, prev linuxproc.CPUStat) float32 {

	/*
	 *    source: http://stackoverflow.com/questions/23367857/accurate-calculation-of-cpu-usage-given-in-percentage-in-linux
	 *
	 *    PrevIdle = previdle + previowait
	 *    Idle = idle + iowait
	 *
	 *    PrevNonIdle = prevuser + prevnice + prevsystem + previrq + prevsoftirq + prevsteal
	 *    NonIdle = user + nice + system + irq + softirq + steal
	 *
	 *    PrevTotal = PrevIdle + PrevNonIdle
	 *    Total = Idle + NonIdle
	 *
	 *    # differentiate: actual value minus the previous one
	 *    totald = Total - PrevTotal
	 *    idled = Idle - PrevIdle
	 *
	 *    CPU_Percentage = (totald - idled)/totald
	 */

	// fmt.Printf("curr:\n %+v\n", curr)
	// fmt.Printf("prev:\n %+v\n", prev)

	PrevIdle := prev.Idle + prev.IOWait
	Idle := curr.Idle + curr.IOWait

	PrevNonIdle := prev.User + prev.Nice + prev.System + prev.IRQ + prev.SoftIRQ + prev.Steal
	NonIdle := curr.User + curr.Nice + curr.System + curr.IRQ + curr.SoftIRQ + curr.Steal

	PrevTotal := PrevIdle + PrevNonIdle
	Total := Idle + NonIdle
	// fmt.Println(PrevIdle, Idle, PrevNonIdle, NonIdle, PrevTotal, Total)

	//  differentiate: actual value minus the previous one
	totald := Total - PrevTotal
	idled := Idle - PrevIdle

	CPU_Percentage := (float32(totald) - float32(idled))*float32(100) / float32(totald)

	return CPU_Percentage
}

```