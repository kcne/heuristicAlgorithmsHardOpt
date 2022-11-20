### The Problem
In the midterm homework assignment we were given 5 different heuristic algorithms to be run with all possible combinations of given parameters. While some of the algorithms included only one parameter, others included 5 or more arrays of different parameter values. After getting all parts of the programs up end running we found out that three of the algorithms given needed more then 24 hours to process only one out of ten objective functions(10% of all combinations). From this arised the question: what is the bottleneck here and how can we may make this faster?

### Bottleneck
Looking at a code we realized that real bottle neck here having massive amount of combinations processing in the sequential order.

````python
# GA main.py
# control logic function
# O(n)
for idx,obj_f in enumerate(obj_functions):

	lower_bound,upper_bound=bounds(idx)
	# O(n^2)
	for pop_size in pop_sizes:
		# O(n^3)
		for num_of_gen in num_of_generations:
			# O(n^3)
			for mut_prob in mut_probs:
				# O(n^4)
				for crossover_type in crossover_types:
					# O(n^5)
					for selection_type in selection_types:
						for _ in range(5):
							# O(n^6)
							sol = GA.GA(args)
````

### Faster, Better, Harder, Stronger
To be able to compute results for a problem like this more efficiently some adjustments have to be made. The main idea behind this approach is instead trying to compute everthing in sequental fashion, design the solution in parallel order and have it execute on multiple threads simultaniously. This way if we run same number of combinations on a **n** multiple threads we can compute the results almost ~**n** times faster. But how many threads do we actually need to make this computable in 24 hours?

### What about the hardware?
Speaking of parallelisation on the hardware end from the architecture perspective one CPU is not really a good fit for this kind of problem. One way of doing this is instead of using CPU to manage all the computations, we can use a GPU to paralell process the main part of the computation(heuristic algorithms).

![[Pasted image 20221121005010.png]]
Figure 1. CPU vs GPU architecture

### We can make everything run faster in C, but what about higher level langauges?
To be able to do this kind of computations, lower level langauges such as C and C++ were standards for a long time until a while ago until higher end GPUs extended their API support for higher level langauges(eg. [CUDA](https://developer.nvidia.com/cuda-toolkit)) as python and python libararies derriving from this starting coming out(eg. [Numba](https://numba.pydata.org/)). 

### Progress on the matter
In a desire to try and develop a better and more efficient solution we made use of [Compute Engine - Google Cloud Platform](https://www.google.com/aclk?sa=l&ai=DChcSEwjmtfm67L37AhVU4ncKHQntCWcYABABGgJlZg&sig=AOD64_1bloMyUUxb5rqhTmKPiu601NZoHA&q&adurl&ved=2ahUKEwjh5fG67L37AhUnR_EDHeYsACoQ0Qx6BAgHEAE) to created virtual enviroment where we are able to run python code on a higher end GPU using [CUDA toolkit](https://developer.nvidia.com/cuda-toolkit) and [Numba](https://numba.pydata.org/). To be able to make use of this libraries -  compile code to be run on GPU and control parallel logic, thread organization and execution tasks, there are still some major adjustments to be made. We are working on them right now while dealing with the library requirments, exploring the docs and implementing changes in a coding repository.

![[Pasted image 20221121015459.png]]
Figure 2. Google Compute Engine Specs

