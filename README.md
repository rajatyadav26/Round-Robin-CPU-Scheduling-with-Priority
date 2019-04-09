#include<stdio.h>
#define maxSize 100
struct process{
	int id,at,bt,pr,rt,comp;
}p[maxSize],t;
int n;
void sort()
{
	//looking arrival time
	int i,j;
	for(i=0;i<n;i++)
	{
		for(j=0;j<n;j++)
		{
			if(p[i].at<p[j].at)
			{
				t=p[i];
				p[i]=p[j];
				p[j]=t;
			}
		}
	}
}
void display()
{
	int i;
	printf("\nPID\tArrtime\tBurst\tPrio");
	for(i=0;i<n;i++)
	{
		printf("\n%d\t%d\t%d\t%d",p[i].id,p[i].at,p[i].bt,p[i].pr);
	}
}
int checkcomplete()
{
	int i;
	for(i=0;i<n;i++)
	{
		if(p[i].comp!=1)
			return 1;
	}
	return 0;	
}
int gethighpriority(int time)
{
	int i,ar[maxSize],len,j,maxpr;
	j=0;
	for(i=0;i<n;i++)
	{
		if(p[i].at<=time && p[i].comp==0)
		{
			ar[j++]=i;
		}
	}
	if(j==0)
	{
		return -1;
	}
	len=j;
	maxpr=ar[0];
	for(j=1;j<len;j++)
	{
		if(maxpr<ar[j])
			maxpr=ar[j];
	}
	return maxpr;
}
int main()
{
	int i,turnaround,wait,tq,time,flag;
	printf("\n Enter the no. of processes : ");
	scanf("%d",&n);
	for(i=0;i<n;i++)
	{
		printf("\nEnter details of Process P%d : ",i);
		p[i].id=i;
		printf("\nArrival Time : ");
		scanf("%d",&p[i].at);
		printf("\nBurst time : ");
		scanf("%d",&p[i].bt);
		p[i].rt=p[i].bt;
		printf("\nPriority : ");
		scanf("%d",&p[i].pr);
		p[i].comp=0;
	}
	sort();
	display();
	turnaround = 0;
	wait = 0;
	printf("\nEnter time quantum : ");
	scanf("%d",&tq);
	time=0;
	i=0;
	do
	{
		if(p[i].at>time)
		{
			while(p[i].at>time)
				time+=1;
			printf(" P_ideal ");	
		}
		else
		{
			if(p[i].rt<tq && p[i].comp==0)
			{
				time+=p[i].bt;
				p[i].comp=1;
				p[i].rt=0;
				wait+=time-p[i].at;
				turnaround+=time-p[i].at-p[i].bt;
			}
			else if(p[i].rt>tq && p[i].comp==0)
			{
				time+=tq;
				p[i].rt=p[i].bt-tq;
				wait+=time-p[i].at;
				turnaround+=time-p[i].at-p[i].bt;
			}
			else if(p[i].rt==tq && p[i].comp==0)
			{
				time+=p[i].rt;
				p[i].comp=1;
				p[i].rt=0;
				wait+=time-p[i].at;
				turnaround+=time-p[i].at-p[i].bt;
			}
			printf(" P%d ",i);
		}
		//i=gethighpriority(time);
		flag=0;
		do
		{
			i=gethighpriority(time);
			if(i==-1)
			{
					time+=1;
					flag=1;
			}	
			
		}
		while(i==-1 && checkcomplete());
		if(flag==1 && checkcomplete())
		{
			printf(" P_ideal ");
		}
	}while(checkcomplete());
	printf("\n The average wait time = %f",(wait/(float)n));
	printf("\n The average turnaround time = %f",(turnaround/(float)n));
}
