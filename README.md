# Round-Robin-Scheduling
The code will take input from users to schedule the processes according to round robin scheduling.
#include <stdio.h>
#define MAX_QUEUE_SIZE 100
typedef struct Processes {
	int process_no;
	int arrival;
	int burst;
} Process;
Process * queue[MAX_QUEUE_SIZE];
int front = 0, rear = -1, processed = 0, curr_time = 0, tq = 10;
int last_front = 0, last_rear = -1;

void enqueue(Process p[], int n) {
	int i, j, can_insert;
	for (i = 0; i < n; ++i)
	{
		can_insert = 1;

		if (p[i].arrival <= curr_time && p[i].burst_time > 0)
		{
			if (front == 0) {
				queue[++rear] = &p[i];
			}
			else
			{
				for (j = last_front; j <= last_rear; ++j) {
					if (queue[j]->process_no == p[i].process_no)
						can_insert = 0;
				}
				if (can_insert == 1)
					queue[++rear] = &p[i];
			}
		}
	}
	for (i = last_front; i <= last_rear; ++i)
	{
		if (queue[i]->burst_time > 0)
			queue[++rear] = queue[i];
	}
}
void execute() {
	int i;

	if (front-1 == rear) {
		printf("CPU idle for 1 second.\n");
		curr_time += 1;
	}
	else {
		last_front = front;
		last_rear = rear;
		for (i = front; i <= rear; ++i, ++front)
		{
			if (queue[i]->burst_time > tq)
			{
				queue[i]->burst_time -= tq;
				curr_time += tq;
				printf("Process number %d executed till %d seconds.\n", queue[i]->process_no, curr_time);
			}
			else if (queue[i]->burst_time > 0)
			{
				curr_time += queue[i]->burst_time;
				queue[i]->burst_time = 0;
				printf("Process number %d executed till %d seconds.\n", queue[i]->process_no, curr_time);
				++processed;
			}
		}
	}
}
int main() {
	int n, i;
	short err_flag = 0;
	do {
		if (err_flag == 1)
			fprintf(stderr,"\nNumber of processes should be greater than 1.\n");
		printf("Enter the number of processes: ");
		scanf("%d", &n);
		err_flag = 1;
	} while (n < 1);
	err_flag = 0;
	Process p[n];
	for (i = 0; i < n; ++i) {
		printf("\n");
		printf("Enter arrival time of process %d: ", i+1);
		scanf("%d", &p[i].arrival);
		printf("Enter burst time of process %d: ", i+1);
		scanf("%d", &p[i].burst);
		p[i].process_no = i+1;
	}

	while (1) {
		enqueue(p, n);
		printf("\nIn queue: ");
		for (i = 0; i <= rear; ++i) {
			printf("%d ", queue[i]->process_no);
		}
		printf("\nFront = %d, Rear = %d.\n\n", front, rear);
		execute();
	
		if (processed == n)
			break;
	}
	return 0;
}
