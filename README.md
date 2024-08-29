# Reproduction for issue [31081](https://github.com/renovatebot/renovate/discussions/31081)

This issue was discovered while using a self-hosted only feature: https://docs.renovatebot.com/self-hosted-configuration/#allowedpostupgradecommands

## Renovate self hosted config
The relevant configuration:

```js
return {
        /*
         * https://docs.renovatebot.com/self-hosted-configuration/#allowedpostupgradecommands
         * A list of regular expressions that decide which post-upgrade tasks are allowed.
         */
        allowedPostUpgradeCommands: ['^echo'],

        /*
         * https://docs.renovatebot.com/self-hosted-configuration/#allowpostupgradecommandtemplating
         * Set this to `false` to disable template compilation for post-upgrade commands.
         */
        allowPostUpgradeCommandTemplating: true,
}
```

## Local Renovate config

```js
{
  $schema: 'https://docs.renovatebot.com/renovate-schema.json',
  packageRules: [

    /*
     * https://docs.renovatebot.com/configuration-options/#postupgradetasks
     * Post-upgrade tasks that are executed before a commit is made by Renovate.
     *
     * Normalises auto-generated files to prevent workflow conflicts and generates a changeset.
     */
    {
      matchDepTypes: [
        'dependencies', 
        'peerDependencies',
      ],
      postUpgradeTasks: {
        commands: [
          'echo \'{{{packageFile}}}\'  \'{{{prTitle}}}\' \'{{{commitMessage}}}\' \'{{{replace \'@\' \'_at_\' depNameSanitized}}}\' > renovate_bug.out',
        ],
        executionMode: 'update',
      },
    },
  ],
}
```

## Details
See pull request: https://github.com/dipunm/renovate-bug-demo/pull/5/files#diff-169d9aaca0523bc2d44b407f8c8b9f6e9149dbddab5f558fd36f9ab2971617cf


This file illustrates that both `packageFile` and `depNameSanitized` template replacements work fine, even with a `replace` modifier. The `prTitle` is empty and `commitMessage` is not properly expanded.

This pull request was created using renovate version `38.9.5` which is the earliest version of the project to behave like this. The previously installed package which does not exhibit this behaviour was version `37.440.7`.