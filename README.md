# Rusty Python Boilerplate

## Description

This is a boilerplate for a Rusty Python project. It is a simple example of how to use Rust from Python.

## Pre-requisite

Ensure [`pip`](https://pip.pypa.io/en/stable/installation/), [`rust`](https://www.rust-lang.org/tools/install), [`huak`](https://github.com/cnpryer/huak/blob/master/docs/user_guide.md), [`maturin`](https://www.maturin.rs/installation) are installed.

## Setup

<details><summary><b>View details:</b></summary>

1. `$ huak new rusty-py-boilerplate`: Create a new project using `huak`.
2. Add some files based on your project. Here, `greet.py`, `maths.py`, `main.py` are added with some code.
3. `$ huak build`: Build the project.
4. Add a guess game module in Rust. In order to do this, follow the steps below:
    1. Create module using `$ cargo new guess-what --lib`.
    2. Go to the `guess-what` directory using `$ cd guess-what`.
    3. Add this code to `Cargo.toml`:

    ```toml
    ...
    ...
    [lib]
    name = "guess_what"
    # "cdylib" is necessary to produce a shared library for Python to import from
    crate-type = ["cdylib"]

    [dependencies]
    rand = "0.8.4"

    [dependencies.pyo3]
    version = "0.20.0"
    # "abi3-py38" tells pyo3 (and maturin) to build using the stable ABI with minimum Python version 3.8
    features = ["abi3-py38"]
    ```

    4. Add `pyproject.toml` file to the root of the project with the following content:

    ```toml
    [build-system]
    requires = ["maturin>=1.0,<2.0"]
    build-backend = "maturin"

    [tool.maturin]
    # "extension-module" tells pyo3 we want to build an extension module (skips linking against libpython.so)
    features = ["pyo3/extension-module"]
    ```

    Now, if you want to try testing this module within a local virtual environment, then try this [step](https://github.com/abhi3700/My_Learning-Python/blob/9829e8828b557b08da0b23e8eb012053aba853b9/libs/py-pm-tools/maturin/README.md#L58-L75) along with.

    5. Add this code to `src/lib.rs` in `src` directory.

    <details><summary><b>Code:</b></summary>

    ```rust
    use pyo3::prelude::*;
    use rand::Rng;
    use std::cmp::Ordering;
    use std::io;

    #[pyfunction]
    fn guess_the_number() {
        println!("Guess the number!");

        let secret_number = rand::thread_rng().gen_range(1..101);

        loop {
            println!("Please input your guess.");

            let mut guess = String::new();

            io::stdin()
                .read_line(&mut guess)
                .expect("Failed to read line");

            let guess: u32 = match guess.trim().parse() {
                Ok(num) => num,
                Err(_) => continue,
            };

            println!("You guessed: {}", guess);

            match guess.cmp(&secret_number) {
                Ordering::Less => println!("Too small!"),
                Ordering::Greater => println!("Too big!"),
                Ordering::Equal => {
                    println!("You win!");
                    break;
                }
            }
        }
    }

    /// A Python module implemented in Rust. The name of this function must match
    /// the `lib.name` setting in the `Cargo.toml`, else Python will not be able to
    /// import the module.
    #[pymodule]
    fn guess_what(_py: Python, m: &PyModule) -> PyResult<()> {
        m.add_function(wrap_pyfunction!(guess_the_number, m)?)?;

        Ok(())
    }
    ```

    </details>

    6. Build the module using `$ maturin develop` or `$ maturin build` (with `.whl` file). This will create a `maturin/` folder with lib in the `target` directory.
5. Come back to the root of the main project using `$ cd ..`. And then run this `python -m pip install ./guess-what` to install the module in your local virtual environment folder i.e. `.venv` during `$ huak build`.
6. Now, you can use the module in your python code. Created a new file `guess.py` and added some code to use the module. Here, tried to call the `guess_the_number` exposed function from the `guess-what` module.
7. Add a task to `pyproject.toml` to run the `guess.py` using `$ huak run guess`.

</details>

That's it for the setup of any rusty-python project using `huak` & `maturin` 🎉.

## Usage

### Build

```sh
huak build
```

### Init

```sh
huak init
```

### Run

There are many programs to try.

| NOTE | We could just have 1 main file instead |
| --- | --- |

```sh
huak run main
huak run greet
huak run maths
huak run guess
```

### Generate `requirements.txt`

```sh
huak freeze
```

> Use the huak with [PR](https://github.com/cnpryer/huak/pull/897) merged. Or else, use this [version of huak](https://github.com/abhi3700/huak).

### Format

```sh
huak fmt
```

### Lint

```sh
huak lint
```

### Test

```sh
huak test
```
