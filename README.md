# Privateer Development 

## Testing

When writing new Python bound functions in C++, it is often a good idea to write Python tests. The idea behind this is to minimise the likelihood of newly introduced code changing breaking or changing the outputs of **unrelated** functions. 

Tests should be run after **every** change to the underlying C++ code. They will be automatically run once pushed to GitHub. 

### Writing Python tests in Privateer

The test directory is structured as follows. 

```
tests
├── expected_outputs
│   └── ClassName
│       ├── function_name.json
├── test_data
│   ├── reference.pdb
├── test_output
│   ├── output.xml
├── test_*.py
```

Folder structure: 
`expected_outputs` - contains text or JSON files of what privateer **should** output if all is well. 
`test_data` - contains standardised PDB, MTZ, mmCIF files which will be used for all testing. 
`test_output` - contains output files that any tested functions will generate. For example, SNFGs generated in Privateer. 
`test_*.py` - Python test files that will be read by pytest. 

To create a test in Privateer, create a Python file which is prefixed with `test_` and with a name which directly relates to the code that it is testing, for example `test_glycosylation_interactions.py`.

In this file, import privateer, pytest and any other relevant modules, for example:

```
from privateer import privateer_core as pvtcore
import json
import pytest
```

Include any global level class instantiation which will be required for testing, for example:

```
model_input = "tests/test_data/5fji.pdb"
model_output = "tests/test_output/5fji_hydrogenated.pdb"

interactions = pvtcore.GlycosylationInteractions(
	path_to_model_file=model_input,
	path_to_output_file=model_output,
	enableHBonds=True
)
```

Note - test files are written relative to the root of Privateer.

To write a test, first define a function prefixed with `test_` followed by the Python function that is being tested, for example `test_get_path_of_model_file_used`. It is also important to add a Python decorator which tells pytest what speed the function will run at, these are custom so further granularity can be added as needed. The current options are: 

- `@pytest.mark.fast`
- `@pytest.mark.slow`

These can be used as follows: 
```
@pytest.mark.fast
def test_get_path_of_model_file_used():
	...
```

The test contents should be simple, i.e. calling a function and inspecting its output. A complete test function is shown, this `get_path_of_model_file_used` function returns a `str` which should be equal to what we passed into the GlycosylationInteraction class keyword argument `path_to_model_file`. We can check this using an **assert**.

```
@pytest.mark.fast
def test_get_path_of_model_file_used():
	result = interactions.get_path_of_model_file_used()
	assert result == model_input
```

assert is a keyword built into Python which checks whether the provided expression evaluates to `True` or `False`. In the case of `True`, the code continues. In the case of `False`, an `AssertionError` is raised.

### Running Python tests in Privateer

Running Python tests in Privateer is straightforward with the pytest module. First ensure that we are in the Privateer python environment (`source privateerpython/bin/activate`).

To run all tests, run: 
	`pytest tests`

To run only a specific test file, run: 
	`pytest tests/test_*.py`

To run only tests marked fast, add the command line flag `-m fast`, e.g: 
	`pytest tests -m fast`
