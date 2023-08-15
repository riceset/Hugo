# 🧐 Philosophers

[Philosophers.pdf](https://res.craft.do/user/full/a56c5213-5c17-64c0-d902-b9958ea11cb4/doc/9BCB3C86-9D37-4BE9-863E-5303AA81C84B/30F76231-2B97-424C-8302-F9F6CB530A9F_2/SBq2mpMyINcnOMyxxfWeyV2Dy8jOMGQ99ME4xUiHgqgz/Philosophers.pdf)

## 👀 Overview

### The Actions

- Eat
- Sleep
- Think

*“When a philosopher has finished eating, they start sleeping. Once awake they think again.”*

### Arguments

- `number_of_philosophers`
- `time_to_die`
- `time_to_eat`
- `time_to_sleep`
- `number_of_times_each_philosopher_must_eat`

### Expected output

```other
timestamp_in_ms X has taken a fork
timestamp_in_ms X is eating
timestamp_in_ms X is sleeping
timestamp_in_ms X is thinking
timestamp_in_ms X died
```

in which `X` is the philosopher ID.

***Note***: The message announcing the death of a philosopher should be displayed in no more than ***::10 ms::*** after the actual death.

## Calculating the time stamps

As the program wants the output in miliseconds and the function `gettimeofday()` gives us values in seconds and microseconds, we have to convert both to miliseconds and sum them up.

### remembering that

$$
1s = 1000ms
$$

$$
1us = \frac{1}{1000} \\[10pt]
\therefore 1us = 0.001ms
$$

Now with all the values in miliseconds, we can sum them up to get the current time in miliseconds.

## Example:

Let’s try converting the following value that is given in seconds to miliseconds.

```cpp
time_t s_to_ms(time_t s)
{
	return (s * 1000);
}
```

$$
1692041923s \\[10pt]
1692041923s \times 1000 = 1692041923000ms
$$

Now, let’s convert the microsecond part to miliseconds (discarding the decimal part).

```cpp
time_t us_to_ms(time_t us)
{
	return (us / 1000);
}
```

$$
126500us \\[10pt]
\frac{126500}{1000}us = 126ms
$$

Summing the seconds and microseconds now converted to miliseconds we get:

$$
1692041923000ms + 126ms = 1692041923126ms
$$

Now, to get the current time you just subtract the gettimeofday() call from the start time that would be the value you saved when calling it for the first time. That would be the code for the conversion made:

```cpp
void get_current_time(time_t *s, time_t *us)
{
	t_timeval tv;

	gettimeofday(&tv, NULL);
	*s = tv.tv_sec;
	*us = tv.tv_usec;
}

int main(void)
{
	time_t s;
	time_t us;

	get_current_time(&s, &us);

	printf("current_time_in_ms:%ld", s_to_ms(s) + us_to_ms(us));
}
```

## Table (the main data structure)

In this project, we will be working with a main data structure in which will contain all the other ones, in the case of the philosophers project, as we will be dealing with threads, more specifically, one thread for each philosopher, we will have to find out how to put this table data structure inside each philosopher for accessing data from each thread.

```javascript
struct s_table
{
	time_t start_time;
	t_args args;
	t_philo **philos;
	pthread_mutex_t *forks;
};
```

In which, the `start_time` is initialized with the actual start time from the program (in miliseconds), `args` are the command line arguments (a.k.a argv, argument vector), `philos` is a double pointer but it might be easier to think about it as a pointer to an array of philosophers, and the `forks` "array".

By the way, I decided creating a new data structure just to store the command line arguments in which I called `t_args`.

```javascript
struct s_args
{
	unsigned nb_philos;
	time_t time_to_die;
	time_t time_to_eat;
	time_t time_to_sleep;
	unsigned must_eat;
};
```

And that's how I initialized the values:

```javascript
static void init_args(t_args *args, int argc, char **argv)
{
	args->nb_philos = philo_atoi(argv[1]);
	args->time_to_die = philo_atoi(argv[2]);
	args->time_to_eat = philo_atoi(argv[3]);
	args->time_to_sleep = philo_atoi(argv[4]);
	if (argc == 6)
		args->must_eat = philo_atoi(argv[5]);
	else
		args->must_eat = MUST_EAT_NOT_SET;
}
```

***note***: `MUST_EAT_NOT_SET` is a macro I defined to -1.

And if you're asking yourself why I didn't typedef any of these structures, that's because I "typedefed" all of them above their declaration in the header file.

```javascript
typedef struct s_table t_table;
typedef struct s_args t_args;
typedef struct s_philo t_philo;
typedef struct timeval t_timeval;
```

## 🍴 Forks

In my table structure I've got a mutex pointer called forks in which it will point to a block of memory containing all the mutexes used in the program.

```javascript
pthread_mutex_t *forks;
```

Then, on my data structure that represents a philosopher I have an array with 2 values. The index 0 of this array represents the philosopher's left fork and the index 1 represents the right fork respectively.

```javascript
unsigned forks[2];
```

***note***: The keyword `unsigned` is an alternative way of writing `unsigned int`.

As I declared my table as a regular variable into main(), I won't need to allocate memory for it.

We will just need to allocate memory for the mutexes.

```javascript
void alloc_forks(t_table *table)
{
  table->forks = malloc(sizeof(pthread_mutex_t) * table->args.nb_philos);
}
```

allocating the desired amount of bytes. (size of a mutex in bytes times the number of philosophers that is the number of forks, in other words, mutexes).

### Initializing the mutexes

after allocating memory to the created mutexes, we will have to initialize them:

```javascript
void init_forks(t_table *table)
{
	unsigned i;

	i = 0;
	alloc_forks(table);
	while (i < table->args.nb_philos)
	{
		pthread_mutex_init(&table->forks[i], NULL);
		i++;
	}
}
```

That's it for allocating and initializing mutexes :)

## Dealing with the philosopher data structure

That's the definition for the philosopher data structure:

```javascript
struct s_philo
{
	pthread_t thread;
	unsigned id;
	unsigned times_ate;
	unsigned forks[2];
	time_t last_meal;
	t_table *table;
};
```

Before doing anything else, we need to of course, allocate memory for the philosophers data structure.

```javascript
void alloc_philos(t_table *table)
{
	unsigned i;

	table->philos = malloc(sizeof(t_philo *) * table->args.nb_philos);
	i = 0;
	while (i < table->args.nb_philos)
	{
		table->philos[i] = malloc(sizeof(t_philo));
		i++;
	}
}
```

First, we allocate the pointers that will point to each philosopher, then using these pointers that were allocated, we allocate the actual memory for each philosopher.

### Initializing the philosophers

```javascript
static void init_philos(t_table *table)
{
	unsigned i;

	i = 0;
	alloc_philos(table);
	while (i < table->args.nb_philos)
	{
		table->philos[i]->id = i + 1;
		table->philos[i]->times_ate = 0;
		table->philos[i]->table = table;
		table->philos[i]->forks[0] = i;
		table->philos[i]->forks[1] = (i + 1) % table->args.nb_philos;
		i++;
	}
}
```

Now let's break down this function.

First, we need to assign an ID to each philosopher and it need to begin at 1. As we are indexing using `i` that usually gets initialized at 0, we need to add 1 to it to get the actual id.

So when `i` is 0, that means, the first philosopher will have the ID $$i + 1$$ that is 1.

The `times_ate` gets an initial value of 0 and we are assigning a pointer to `table` to have a reference inside each thread created later.

### Assigning forks

The current philosopher's left fork will be `i`. So for example, when $$i = 0$$, the philosopher's ID will be 1 so their left fork will be 0 and their right fork gotta be 1.

To get to the value of 1 we need to index it using the following formula:

$$
(i + 1) \mod n
$$

So imagine we have 2 philosophers ($$n = 2$$), the first philosopher's right fork will represent the following index:

$$
(0 + 1) \mod 2 \\[10pt]
= 1 \mod 2 \\[10pt]
= 1 \\[10pt]
$$

The second philosopher will have:

$$
(1 + 1) \mod 2 \\[10pt]
= 2 \mod 2 \\[10pt]
= 0 \\[10pt]
$$

So the first philosopher's left fork will be 0, and the right fork will be 1, while the second philosopher's left fork will be 1 and the right fork will be 0 again. We just succeeded in simulating a round table with forks in between each philosopher.

![Drawing](https://res.craft.do/user/preview/a56c5213-5c17-64c0-d902-b9958ea11cb4/doc/9BCB3C86-9D37-4BE9-863E-5303AA81C84B/4DE81475-B9BB-4B1F-AC51-8AFD3C416B8A_1/4aMSLV7rtyUkDXHAUXORo5u5POCWDIyqsbGsLMoZV4kz/Drawing.jpg)

## 🚀 References

### My repository

[GitHub - riceset/philosophers: In this project, you will learn the basics of threading a process. You will see how to create threads and you will discover mutexes.](https://github.com/riceset/philosophers)