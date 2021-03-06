# Step Development Guideline

## Never depend on Environment Variables in your Step

You should expose every outside variable as an input of your step,
and just set the default value to the Environment Variable you want to use in the `step.yml`.

An example:

The Xcode Archive step generates a `$BITRISE_IPA_PATH` output environment variable.
**You should not** use this environment variable in your Step's code directly,
instead you should declare an input for your Step in `step.yml` and just set the default
value to `$BITRISE_IPA_PATH`. Example:

```
- ipa_path: "$BITRISE_IPA_PATH"
  opts:
      title: "IPA path"
```

After this, in your Step's code you can expect that the `$ipa_path` Environment Variable will
contain the value of the IPA path.

By declaring every option as an input you make it easier to test your Step,
and you also let the user of your Step to easily declare these inputs,
instead of searching in the code for the required Environment Variable.


## Do not use submodules, or require any other resource downloaded on-demand

As a Step runs frequently in a CI / automation environment you should try to make your Step as stable as possible.
This includes the resources / tools used by your Step as well, not just the core code.

If your Step depends on another tool, which have to be downloaded on-demand, during the execution
of your Step, there's a chance that even your Step was retrieved correctly but the
resource it tries to download just fails because of a network, authorization or other error.

You should try to include everything what's required for your Step into the Step's repository.
In case of submodules, you should rather include the content of the other repository,
instead of actually using it as a submodule.

The only exception is the dependencies you can fetch from an OS dependency manager,
on Debian systems you can use `apt-get` and on OS X you can use `brew`.
You can declare these dependencies in your `step.yml`, with the `deps` property,
and `bitrise` will manage to call the dependency manager to install the dependency,
and will fail before the Step execution in case it can't retrieve the dependency.
