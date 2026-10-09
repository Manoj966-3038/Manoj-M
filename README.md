# Manoj-Mdef fibonacci_generator():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b


# Example: Print the first 10 numbers using the generator
fib = fibonacci_generator()
print([next(fib) for _ in range(10)])
