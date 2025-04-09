# Actividad de seguimiento - Simulación 1

| Integrante                   | correo                   | usuario github          |
| ---------------------------- | ------------------------ | ----------------------- |
| John Edison Zapata Ramirez   | john.zapata1@udea.edu.co | John3814                |

## Instrucciones

Antes de empezar a realizar esta actividad haga un **fork** de este repositorio y sobre este trabaje en la solución de las preguntas planteadas en la actividad de simulación. Las respuestas deben ser respondidas en español o si lo prefiere en ingles en el lugar señalado para ello (La palabra **answer** muestra donde).

**Importante**:

- Como la actividad es en las parejas del laboratorio, solo uno de los integrantes tiene que hacer el fork; y sobre repositorio bifurcado que se genera, la modificación se realiza en equipo.
- Como la entrega se debe hacer modificando el archivo READNE, se recomienda que consulte mas sobre el lenguaje **Markdown**. En el repo adjuntan dos cheatsheet ([cheat sheet 1](Markdown_Cheat_Sheet.pdf), [cheatsheet 2](markdown-cheatsheet.pdf)) para consulta rapida.
- Entre mas creativo mejor.

## Homework (Simulation)

This program, [`process-run.py`](process-run.py), allows you to see how process states change as programs run and either use the CPU (e.g., perform an add instruction) or do I/O (e.g., send a request to a disk and wait for it to complete). See the [README](https://github.com/remzi-arpacidusseau/ostep-homework/blob/master/cpu-intro/README.md) for details.

### Questions

1. Run `process-run.py` with the following flags: `-l 5:100,5:100`. What should the CPU utilization be (e.g., the percent of time the CPU is in use?) Why do you know this? Use the `-c` and `-p` flags to see if you were right.


   <details>
   <summary>Answer</summary>
   As in the process list, we can see there are two processes, each with 5 `X` instructions, and with a CPU utilization percentage for both of them of 100% `Y`, i.e., there are no I/O instructions. Therefore, the total CPU utilization should be 100%.
   <br><br>
   This is confirmed by running the program like this:
   <br><br>

      | Time | PID: 0     | PID: 1     | CPU   | IOs    |
      |------|------------|------------|-------|--------|
      | 1    | RUN:cpu    | READY      | 1     |        |
      | 2    | RUN:cpu    | READY      | 1     |        |
      | 3    | RUN:cpu    | READY      | 1     |        |
      | 4    | RUN:cpu    | READY      | 1     |        |
      | 5    | RUN:cpu    | READY      | 1     |        |
      | 6    | DONE       | RUN:cpu    | 1     |        |
      | 7    | DONE       | RUN:cpu    | 1     |        |
      | 8    | DONE       | RUN:cpu    | 1     |        |
      | 9    | DONE       | RUN:cpu    | 1     |        |
      | 10   | DONE       | RUN:cpu    | 1     |        |

      **Stats:**  
      - **Total Time:** 10  
      - **CPU Busy:** 10 (100.00%)  
      - **IO Busy:** 0 (0.00%)  
      <br><br>
      ![CPU-VS-I/O](./img/1/cpu-vs-io.png)
      <br><br>
   </details>
   <br>

2. Now run with these flags: `./process-run.py -l 4:100,1:0`. These flags specify one process with 4 instructions (all to use the CPU), and one that simply issues an I/O and waits for it to be done. How long does it take to complete both processes? Use `-c` and `-p` to find out if you were right.

   <details>
   <summary>Answer</summary>
   For the first process, there are 4 instructions, all using the CPU exclusively, with no input/output (I/O) operations. Therefore, this process takes 4 time units to complete.<br><br>

   As for the second process, it consists of an I/O operation. This means that it initially uses the CPU to call the I/O instruction, and then enters the BLOCKED state. In this state, by default, it takes 5 time units to complete the operation. Once completed, the process returns to the CPU (if it is free) to complete execution. In total, this process takes 7 time units (2 on the CPU and 5 in the BLOCKED state).<br><br>

   Finally, we can deduce that it takes 11 time units to complete both processes.
   <br><br>

      | Time | PID: 0         | PID: 1           | CPU Busy | IO Busy |
      |------|-----------------|------------------|----------|---------|
      | 1    | RUN:cpu        | READY            | 1        | 0       |
      | 2    | RUN:cpu        | READY            | 1        | 0       |
      | 3    | RUN:cpu        | READY            | 1        | 0       |
      | 4    | RUN:cpu        | READY            | 1        | 0       |
      | 5    | DONE           | RUN:io           | 1        | 0       |
      | 6    | DONE           | BLOCKED          | 0        | 1       |
      | 7    | DONE           | BLOCKED          | 0        | 1       |
      | 8    | DONE           | BLOCKED          | 0        | 1       |
      | 9    | DONE           | BLOCKED          | 0        | 1       |
      | 10   | DONE           | BLOCKED          | 0        | 1       |
      | 11   | DONE           | RUN:io_done      | 1        | 0       |

      #### Estadísticas
      - Total Time: **11**
      - CPU Busy: **6** (54.55%)
      - IO Busy: **5** (45.45%)
   <br><br>
   ![CPU-vs-IO](./img/2/cpu-vs-io.png)
   <br><br>
   </details>
   <br>

3. Switch the order of the processes: `-l 1:0,4:100`. What happens now? Does switching the order matter? Why? (As always, use `-c` and `-p` to see if you were right)

   <details>
   <summary>Answer</summary>

   It does matter, since both processes execute in less time. That is, each one individually takes the same amount of time as in the previous case, but together they take much less time.<br><br>

   This is because, while the process with I/O instructions is in the BLOCKED state, the other process can take advantage of the free CPU to execute its instructions. This allows for better CPU utilization and allows both processes to complete their execution much faster.<br><br>


   | Time | PID: 0         | PID: 1           | CPU Busy | IO Busy |
   |------|-----------------|------------------|----------|---------|
   | 1    | RUN:io         | READY            | 1        | 0       |
   | 2    | BLOCKED        | RUN:cpu          | 1        | 1       |
   | 3    | BLOCKED        | RUN:cpu          | 1        | 1       |
   | 4    | BLOCKED        | RUN:cpu          | 1        | 1       |
   | 5    | BLOCKED        | RUN:cpu          | 1        | 1       |
   | 6    | BLOCKED        | DONE             | 0        | 1       |
   | 7    | RUN:io_done    | DONE             | 1        | 0       |

   #### Estadísticas
   - **Total Time:** **7** unidades de tiempo.
   - **CPU Busy:** **6** unidades (85.71%).
   - **IO Busy:** **5** unidades (71.43%).

   <br><br>
   ![CPU-VS-I/O](./img/3/cpu-vs-io.png)
   <br><br>
   </details>
   <br>

4. We'll now explore some of the other flags. One important flag is `-S`, which determines how the system reacts when a process issues an I/O. With the flag set to SWITCH ON END, the system will NOT switch to another process while one is doing I/O, instead waiting until the process is completely finished. What happens when you run the following two processes (`-l 1:0,4:100 -c -S SWITCH ON END`), one doing I/O and the other doing CPU work?

   <details>
   <summary>Answer</summary>
   Given the behavior defined by SWITCH ON END and the process list, even if the process that performs a single I/O call is executed first, the cost will be the same as if the CPU-only process were executed first. This results in a longer time required to complete both processes, in addition to wasting CPU usage based on the available time.
   <br><br>

      | Time | PID: 0         | PID: 1           | CPU Busy | IO Busy |
      |------|-----------------|------------------|----------|---------|
      | 1    | RUN:io         | READY            | 1        | 0       |
      | 2    | BLOCKED        | READY            | 0        | 1       |
      | 3    | BLOCKED        | READY            | 0        | 1       |
      | 4    | BLOCKED        | READY            | 0        | 1       |
      | 5    | BLOCKED        | READY            | 0        | 1       |
      | 6    | BLOCKED        | READY            | 0        | 1       |
      | 7    | RUN:io_done    | READY            | 1        | 0       |
      | 8    | DONE           | RUN:cpu          | 1        | 0       |
      | 9    | DONE           | RUN:cpu          | 1        | 0       |
      | 10   | DONE           | RUN:cpu          | 1        | 0       |
      | 11   | DONE           | RUN:cpu          | 1        | 0       |

      #### Estadísticas
      - **Total Time:** **11** unidades de tiempo.
      - **CPU Busy:** **6** unidades (54.55%).
      - **IO Busy:** **5** unidades (45.45%).

      <br><br>
      ![CPU-VS-I/O](./img/4/cpu-vs-io.png)
      <br><br>
   </details>
   <br>

5. Now, run the same processes, but with the switching behavior set to switch to another process whenever one is WAITING for I/O (`-l 1:0,4:100 -c -S SWITCH ON IO`). What happens now? Use `-c` and `-p` to confirm that you are right.

   <details>
   <summary>Answer</summary>
   In this case, the behavior defined by the SWITCH_ON_IO instruction is equivalent to the second case, as it specifies that the system will switch to another process when one is executing an input/output (I/O) instruction. With the same two processes running, the total time required to complete them is 7 units.<br><br>

   This model allows for better CPU utilization based on the available time. Furthermore, with a larger number of processes, the efficiency improvement would be more significant.<br><br>
   
      | Time | PID: 0         | PID: 1           | CPU Busy | IO Busy |
      |------|-----------------|------------------|----------|---------|
      | 1    | RUN:io         | READY            | 1        | 0       |
      | 2    | BLOCKED        | RUN:cpu          | 1        | 1       |
      | 3    | BLOCKED        | RUN:cpu          | 1        | 1       |
      | 4    | BLOCKED        | RUN:cpu          | 1        | 1       |
      | 5    | BLOCKED        | RUN:cpu          | 1        | 1       |
      | 6    | BLOCKED        | DONE             | 0        | 1       |
      | 7    | RUN:io_done    | DONE             | 1        | 0       |

      #### Estadísticas
      - **Total Time:** **7** unidades de tiempo.
      - **CPU Busy:** **6** unidades (85.71%).
      - **IO Busy:** **5** unidades (71.43%).
      <br><br>
      ![CPU-VS-I/O](./img/5/cpu-vs-io.png)
      <br><br>
   </details>
   <br>

6. One other important behavior is what to do when an I/O completes. With `-I IO RUN LATER`, when an I/O completes, the process that issued it is not necessarily run right away; rather, whatever was running at the time keeps running. What happens when you run this combination of processes? (`./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH ON IO -c -p -I IO RUN LATER`) Are system resources being effectively utilized?

   <details>
   <summary>Answer</summary>
   In this specific case, as described for the IO_RUN_LATER parameter and based on the process list, the following happens: first, process 0 executes its first I/O instruction, which causes it to enter the BLOCKED state. While process 0 is in this state and the CPU is free, process 1's instructions, which are CPU-only (5 instructions), begin executing.<br><br>

   When process 0 completes its I/O instruction, it is not executed immediately, but moves to the end of the queue in the READY state. It can only execute again when all the processes ahead in the queue have finished. In this case, this means that it cannot execute another I/O instruction until the other processes finish.<br><br>

   Finally, when process 0 can execute its instructions again, since the other processes (which only have CPU-only instructions) have already finished, each time it enters the BLOCKED state, the CPU is left unused. This results in a waste of available resources and reduced performance in terms of time efficiency.
   <br><br>

   | Time | PID: 0         | PID: 1           | PID: 2           | PID: 3           | CPU Busy | IO Busy |
   |------|-----------------|------------------|------------------|------------------|----------|---------|
   | 1    | RUN:io         | READY            | READY            | READY            | 1        | 0       |
   | 2    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
   | 3    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
   | 4    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
   | 5    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
   | 6    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
   | 7    | READY          | DONE             | RUN:cpu          | READY            | 1        | 0       |
   | 8    | READY          | DONE             | RUN:cpu          | READY            | 1        | 0       |
   | 9    | READY          | DONE             | RUN:cpu          | READY            | 1        | 0       |
   | 10   | READY          | DONE             | RUN:cpu          | READY            | 1        | 0       |
   | 11   | READY          | DONE             | RUN:cpu          | READY            | 1        | 0       |
   | 12   | READY          | DONE             | DONE             | RUN:cpu          | 1        | 0       |
   | 13   | READY          | DONE             | DONE             | RUN:cpu          | 1        | 0       |
   | 14   | READY          | DONE             | DONE             | RUN:cpu          | 1        | 0       |
   | 15   | READY          | DONE             | DONE             | RUN:cpu          | 1        | 0       |
   | 16   | READY          | DONE             | DONE             | RUN:cpu          | 1        | 0       |
   | 17   | RUN:io_done    | DONE             | DONE             | DONE             | 1        | 0       |
   | 18   | RUN:io         | DONE             | DONE             | DONE             | 1        | 0       |
   | 19   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 20   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 21   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 22   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 23   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 24   | RUN:io_done    | DONE             | DONE             | DONE             | 1        | 0       |
   | 25   | RUN:io         | DONE             | DONE             | DONE             | 1        | 0       |
   | 26   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 27   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 28   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 29   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 30   | BLOCKED        | DONE             | DONE             | DONE             | 0        | 1       |
   | 31   | RUN:io_done    | DONE             | DONE             | DONE             | 1        | 0       |

   #### Estadísticas
   - **Total Time:** **31** unidades de tiempo.
   - **CPU Busy:** **21** unidades (67.74%).
   - **IO Busy:** **15** unidades (48.39%).
   <br><br>
   ![CPU-VS-I/O](./img/6/cpu-vs-io.png)
   <br><br>
   </details>
   <br>

7. Now run the same processes, but with `-I IO RUN IMMEDIATE` set, which immediately runs the process that issued the I/O. How does this behavior differ? Why might running a process that just completed an I/O again be a good idea?

   <details>
   <summary>Answer</summary>   
   In this case, the main difference lies in the behavior defined by the IO_RUN_IMMEDIATE parameter. When a process completes an input/output (I/O) instruction, it immediately proceeds to execute the next instruction on the CPU, without having to wait in the READY process queue. This allows process 0, which has multiple I/O instructions, to execute immediately each time it completes an instruction. <br><br>

   While process 0 is in the BLOCKED state, other processes can take advantage of the CPU availability to execute their own instructions. This results in more efficient CPU utilization, significantly reducing the total time required to complete all processes. This behavior improves resource utilization.
   <br><br>

      | Time | PID: 0         | PID: 1           | PID: 2           | PID: 3           | CPU Busy | IO Busy |
      |------|-----------------|------------------|------------------|------------------|----------|---------|
      | 1    | RUN:io         | READY            | READY            | READY            | 1        | 0       |
      | 2    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
      | 3    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
      | 4    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
      | 5    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
      | 6    | BLOCKED        | RUN:cpu          | READY            | READY            | 1        | 1       |
      | 7    | RUN:io_done    | DONE             | READY            | READY            | 1        | 0       |
      | 8    | RUN:io         | DONE             | READY            | READY            | 1        | 0       |
      | 9    | BLOCKED        | DONE             | RUN:cpu          | READY            | 1        | 1       |
      | 10   | BLOCKED        | DONE             | RUN:cpu          | READY            | 1        | 1       |
      | 11   | BLOCKED        | DONE             | RUN:cpu          | READY            | 1        | 1       |
      | 12   | BLOCKED        | DONE             | RUN:cpu          | READY            | 1        | 1       |
      | 13   | BLOCKED        | DONE             | RUN:cpu          | READY            | 1        | 1       |
      | 14   | RUN:io_done    | DONE             | DONE             | READY            | 1        | 0       |
      | 15   | RUN:io         | DONE             | DONE             | READY            | 1        | 0       |
      | 16   | BLOCKED        | DONE             | DONE             | RUN:cpu          | 1        | 1       |
      | 17   | BLOCKED        | DONE             | DONE             | RUN:cpu          | 1        | 1       |
      | 18   | BLOCKED        | DONE             | DONE             | RUN:cpu          | 1        | 1       |
      | 19   | BLOCKED        | DONE             | DONE             | RUN:cpu          | 1        | 1       |
      | 20   | BLOCKED        | DONE             | DONE             | RUN:cpu          | 1        | 1       |
      | 21   | RUN:io_done    | DONE             | DONE             | DONE             | 1        | 0       |

      #### Estadísticas
      - **Total Time:** **21** unidades de tiempo.
      - **CPU Busy:** **21** unidades (100.00%).
      - **IO Busy:** **15** unidades (71.43%).
      <br><br>
      ![CPU-VS-I/O](./img/7/cpu-vs-io.png)
      <br><br>
   </details>
   <br>

### Criterios de evaluación

- [x] Despligue de los resultados y analisis claro de los resultados respecto a lo visto en la teoria.
- [x] Creatividad y orden.
