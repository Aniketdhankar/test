Below is a **clean new Markdown file** containing **ONLY Programs 1, 2, 3, 5, and 6**, extracted from your PDF (pages cited), and also including the two programs you pasted (MPI_Bcast + OpenMP Prime Program).
Citations from the PDF are included.

---

# **Parallel Computing Programs – Extracted & Cleaned (Programs 1, 2, 3, 5, 6)**

*Source: Parallel Computing Lab Manual* 

---

# **📌 Program 1 — Parallel Merge Sort using OpenMP Sections**

*Reference: PDF pages 16–20* 

```c
#include <stdio.h>
#include <stdlib.h>
#include <omp.h>
#include <time.h>

// Merge two halves
void merge(int* arr, int l, int m, int r) {
    int i, j, k;
    int n1 = m - l + 1, n2 = r - m;
    int* L = malloc(n1 * sizeof(int));
    int* R = malloc(n2 * sizeof(int));

    for (i = 0; i < n1; i++) L[i] = arr[l + i];
    for (j = 0; j < n2; j++) R[j] = arr[m + 1 + j];

    i = j = 0;
    k = l;

    while (i < n1 && j < n2)
        arr[k++] = (L[i] <= R[j]) ? L[i++] : R[j++];

    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];

    free(L); free(R);
}

// Sequential MergeSort
void mergeSortSequential(int* arr, int l, int r) {
    if (l < r) {
        int m = (l + r) / 2;
        mergeSortSequential(arr, l, m);
        mergeSortSequential(arr, m + 1, r);
        merge(arr, l, m, r);
    }
}

// Parallel MergeSort
void mergeSortParallel(int* arr, int l, int r, int depth) {
    if (l < r) {
        int m = (l + r) / 2;

        if (depth <= 0) {
            mergeSortSequential(arr, l, m);
            mergeSortSequential(arr, m + 1, r);
        } else {
            #pragma omp parallel sections
            {
                #pragma omp section
                mergeSortParallel(arr, l, m, depth - 1);

                #pragma omp section
                mergeSortParallel(arr, m + 1, r, depth - 1);
            }
        }
        merge(arr, l, m, r);
    }
}

int main() {
    int n = 1000000;
    int *arrSeq = malloc(n * sizeof(int));
    int *arrPar = malloc(n * sizeof(int));

    srand(42);
    for (int i = 0; i < n; i++) {
        arrSeq[i] = rand() % 100000;
        arrPar[i] = arrSeq[i];
    }

    double start = omp_get_wtime();
    mergeSortSequential(arrSeq, 0, n - 1);
    double end = omp_get_wtime();
    printf("Sequential time: %.6f\n", end - start);

    start = omp_get_wtime();
    mergeSortParallel(arrPar, 0, n - 1, 4);
    end = omp_get_wtime();
    printf("Parallel time: %.6f\n", end - start);

    free(arrSeq); free(arrPar);
    return 0;
}
```

---

# **📌 Program 2 — OpenMP Static Scheduling (static,2)**

*Reference: PDF pages 21–22* 

```c
#include <stdio.h>
#include <omp.h>

int main() {
    int num_iterations;

    printf("Enter iterations: ");
    scanf("%d", &num_iterations);

    printf("\nUsing schedule(static,2):\n");

    #pragma omp parallel
    {
        int tid = omp_get_thread_num();

        #pragma omp for schedule(static, 2)
        for(int i = 0; i < num_iterations; i++) {
            printf("Thread %d : Iteration %d\n", tid, i);
        }
    }

    return 0;
}
```

---

# **📌 Program 3 — Fibonacci Using OpenMP Tasks**

*Reference: PDF pages 23–24* 

```c
#include <stdio.h>
#include <stdlib.h>
#include <omp.h>

int fib(int n) {
    if (n <= 1) return n;

    int x, y;

    #pragma omp task shared(x)
    x = fib(n - 1);

    #pragma omp task shared(y)
    y = fib(n - 2);

    #pragma omp taskwait
    return x + y;
}

int main() {
    int n;

    printf("Enter n: ");
    scanf("%d", &n);

    printf("Fibonacci numbers:\n");

    double start = omp_get_wtime();

    #pragma omp parallel
    {
        #pragma omp single
        {
            for (int i = 0; i < n; i++) {
                int result;

                #pragma omp task shared(result)
                {
                    result = fib(i);

                    #pragma omp critical
                    printf("Fib(%d) = %d\n", i, result);
                }
            }
        }
    }

    double end = omp_get_wtime();
    printf("Time: %.6f seconds\n", end - start);

    return 0;
}
```

---

# **📌 Program 5 — MPI_Send & MPI_Recv Demonstration**

*Reference: PDF pages 29–31* 

```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {
    int rank, size, number;

    MPI_Init(&argc, &argv);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Comm_size(MPI_COMM_WORLD, &size);

    if (size < 2) {
        if (rank == 0)
            printf("Requires at least 2 processes.\n");

        MPI_Finalize();
        return 0;
    }

    if (rank == 0) {
        number = 100;
        printf("Process 0 sending number %d\n", number);
        MPI_Send(&number, 1, MPI_INT, 1, 0, MPI_COMM_WORLD);
    }
    else if (rank == 1) {
        MPI_Recv(&number, 1, MPI_INT, 0, 0, MPI_COMM_WORLD, MPI_STATUS_IGNORE);
        printf("Process 1 received %d\n", number);
    }

    MPI_Finalize();
    return 0;
}
```

---

# **📌 Program 6 — MPI Deadlock & Deadlock Avoidance**

*Reference: PDF pages 32–33* 

```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {

    int rank, size;
    int msg_send = 100, msg_recv;

    MPI_Init(&argc, &argv);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Comm_size(MPI_COMM_WORLD, &size);

    if (size < 2) {
        if (rank == 0)
            printf("Run with at least 2 processes.\n");

        MPI_Finalize();
        return 0;
    }

    if (rank == 0) {
        printf("P0 sending to P1\n");
        MPI_Send(&msg_send, 1, MPI_INT, 1, 0, MPI_COMM_WORLD);
        MPI_Recv(&msg_recv, 1, MPI_INT, 1, 0, MPI_COMM_WORLD, MPI_STATUS_IGNORE);
        printf("P0 received: %d\n", msg_recv);
    }
    else if (rank == 1) {
        printf("P1 sending to P0\n");
        MPI_Send(&msg_send, 1, MPI_INT, 0, 0, MPI_COMM_WORLD);
        MPI_Recv(&msg_recv, 1, MPI_INT, 0, 0, MPI_COMM_WORLD, MPI_STATUS_IGNORE);
        printf("P1 received: %d\n", msg_recv);
    }

    MPI_Finalize();
    return 0;
}
```

---

# ✅ **Additional Programs You Provided**

## **MPI_Bcast Program**

```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {
    int rank, size, number;

    MPI_Init(&argc, &argv);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Comm_size(MPI_COMM_WORLD, &size);

    if (rank == 0) {
        printf("Enter number to broadcast: ");
        scanf("%d", &number);
    }

    MPI_Bcast(&number, 1, MPI_INT, 0, MPI_COMM_WORLD);

    printf("Process %d of %d received %d\n", rank, size, number);

    MPI_Finalize();
    return 0;
}
```

---

## **OpenMP Serial + Parallel Prime Finder**

```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <omp.h>

// Check if number is prime
int is_prime(int num) {
    if (num <= 1) return 0;
    if (num == 2) return 1;
    if (num % 2 == 0) return 0;

    int limit = sqrt(num);
    for (int i = 3; i <= limit; i += 2)
        if (num % i == 0) return 0;

    return 1;
}

int main() {
    int n;
    printf("Enter upper limit: ");
    scanf("%d", &n);

    // SERIAL
    double start_s = omp_get_wtime();
    int* primes_s = malloc((n + 1) * sizeof(int));
    int count_s = 0;

    for (int i = 2; i <= n; i++)
        if (is_prime(i))
            primes_s[count_s++] = i;

    double end_s = omp_get_wtime();

    printf("\nSerial primes: %d\n", count_s);
    printf("Serial time: %.6f\n", end_s - start_s);

    // PARALLEL
    double start_p = omp_get_wtime();
    int* primes_p = malloc((n + 1) * sizeof(int));
    int count_p = 0;

    #pragma omp parallel
    {
        int* local_p = malloc((n + 1) * sizeof(int));
        int local_c = 0;

        #pragma omp for nowait
        for (int i = 2; i <= n; i++)
            if (is_prime(i))
                local_p[local_c++] = i;

        #pragma omp critical
        for (int i = 0; i < local_c; i++)
            primes_p[count_p++] = local_p[i];

        free(local_p);
    }

    double end_p = omp_get_wtime();

    printf("\nParallel primes: %d\n", count_p);
    printf("Parallel time: %.6f\n", end_p - start_p);

    return 0;
}
```

---

If you want, I can now:

📌 Combine all these into **one big PDF**
📌 Create **separate .md files** for each experiment
📌 Format with headings/output screenshots
📌 Add explanations + diagrams

Just tell me!
