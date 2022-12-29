---
title: "Concepts"
date: 2022-08-16T17:21:40-06:00
draft: false
weight: 2
---
Move2Kube has four concepts that are useful to understand when customizing output.

> **Important:** It may be helpful to go through the tutorials first, then reading this section to learn more about each concept in more detail.

## Artifacts
[Source code](https://github.com/konveyor/move2kube/blob/6448624d79c37809417c05e34fcb3b2456952bcb/types/transformer/artifact.go#L37-L45)

Artifacts represent the application objects that can be passed between transformers.
```
type Artifact struct {
	Name        string               `yaml:"name,omitempty" json:"name,omitempty"`
	Type        ArtifactType         `yaml:"type,omitempty" json:"type,omitempty"`
	ProcessWith metav1.LabelSelector `yaml:"processWith,omitempty" json:"processWith,omitempty"` // Selector for choosing transformers that should process this artifact, empty is everything

	Paths   map[PathType][]string      `yaml:"paths,omitempty" json:"paths,omitempty" m2kpath:"normal"`
	Configs map[ConfigType]interface{} `yaml:"configs,omitempty" json:"config,omitempty"` // Could be IR or template config or any custom configuration
}
```
### Artifact fields
Each artifact is an object with fields that need to be understood in order to write transformers effectively.

- `name` : `string` - Name of the artifact.
- `type` : `string` - Type of the artifact (any artifact type can be used).

> **Important:** Transformers consume artifacts based on their type, so custom artifact types can only be consumed by custom transformers that understand them. Example built-in artifact types include: `IR`, `KubernetesYamls`, `Dockerfile`, etc.

- `processWith` : `object` - This is the same as the Kubernetes label selector field. See https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#resources-that-support-set-based-requirements
- `paths` : `object ([string]: []string)` - Mapping from a file type to a list of directories containing files of that type.
    - The key is a string containing the file type.
    - The value is a list of strings/paths to directories containing files of that type.
- `configs` : `object ([string]: any)` - Mapping between different types of configurations and the configuration data.
    - The key is a string containing the type of configuration.
    - The value can be anything, but usually an object. Example built-in configs:
        - [types/transformer/artifacts/cloudfoundry.go#L35-L39](https://github.com/konveyor/move2kube/blob/dcf8793a889c0a8f9f4423e9e9ee3a95003c6bcc/types/transformer/artifacts/cloudfoundry.go#L35-L39)
        - [types/transformer/artifacts/java.go#L49-L75](https://github.com/konveyor/move2kube/blob/dcf8793a889c0a8f9f4423e9e9ee3a95003c6bcc/types/transformer/artifacts/java.go#L49-L75)
        - [types/transformer/artifacts/gradle.go#L24-L27](https://github.com/konveyor/move2kube/blob/dcf8793a889c0a8f9f4423e9e9ee3a95003c6bcc/types/transformer/artifacts/gradle.go#L24-L27)

## Transformers
Move2Kube uses transformers to modify input into the desired output form. Each transformer consumes artifacts as input and returns output artifacts and PathMappings.

The artifacts allow multiple transformers to be chained together to achieve an end to end transformation and the PathMappings are used for persisting the changes in the file system. Some transformers have detection capability to go through the source directories to identify once it understands and creates new artifacts to start the process.

### Transformer directories
Each transformer generally has its own directory with all the configuration parameters required for that transformer whether it is a [built-in transformer](https://github.com/konveyor/move2kube/tree/main/assets/built-in/transformers) or [external transformer](https://github.com/konveyor/move2kube-transformers). The transformer YAML is the most important part of the definition because it specifies its behavior. It also can have a `templates` directory for template files to be used by the transformer, and other files/configurations that are specific to each transformer.

### Transformer YAML
[Source code](https://github.com/konveyor/move2kube/blob/6448624d79c37809417c05e34fcb3b2456952bcb/types/transformer/transformer.go#L27-L49)

Transformers define the definition of the Cloud Foundry (CF) runtime instance app file.
```
type Transformer struct {
    types.TypeMeta   `yaml:",inline" json:",inline"`
    types.ObjectMeta `yaml:"metadata,omitempty" json:"metadata,omitempty"`
    Spec             TransformerSpec `yaml:"spec,omitempty" json:"spec,omitempty"`
}
```
TransformerSpec stores the data.
```
type TransformerSpec struct {
    FilePath           string                                 `yaml:"-" json:"-"`
    Class              string                                 `yaml:"class" json:"class"`
    Isolated           bool                                   `yaml:"isolated" json:"isolated"`
    DirectoryDetect    DirectoryDetect                        `yaml:"directoryDetect" json:"directoryDetect"`
    ExternalFiles      map[string]string                      `yaml:"externalFiles" json:"externalFiles"` // [source]destination
    ConsumedArtifacts  map[ArtifactType]ArtifactProcessConfig `yaml:"consumes" json:"consumes"`
    ProducedArtifacts  map[ArtifactType]ProducedArtifact      `yaml:"produces" json:"produces"`
    Dependency         interface{}                            `yaml:"dependency" json:"dependency"` // metav1.LabelSelector
    Override           interface{}                            `yaml:"override" json:"override"`     // metav1.LabelSelector
    DependencySelector labels.Selector                        `yaml:"-" json:"-"`
    OverrideSelector   labels.Selector                        `yaml:"-" json:"-"`
    TemplatesDir       string                                 `yaml:"templates" json:"templates"` // Relative to yaml directory or working directory in image
    Config             interface{}                            `yaml:"config" json:"config"`
}
```

The example above shows the format of the transformer YAML file. For more information on the YAML format, see this [quick tutorial](https://www.redhat.com/en/topics/automation/what-is-yaml).   

YAML files have four main fields with sub-fields that define them.

> **Note:** The `apiVersion` and `kind` are necessary to tell Move2Kube that this is a transformer.

- `apiVersion` : `string` - Similar to Kubernetes apiVersion strings. This should be `move2kube.konveyor.io/v1alpha1` for now.
- `kind` : `string` - Resource type contained in the YAML file which should be labeled `Transformer` for transformers.
- `metadata` : `object` - Defines the transformer name and can set optional labels used to enable or disable the transformer.
    - `name` : `string` - Transformer name.
    - `labels` : `object ([string]: string)` - Set of labels similar to Kubernetes used to enable/disable a set of transformers during both planning and transformation phases. For more details run the `move2kube help transform` command.
- `spec` : `object` - Main transformer data.
    - `class` : `string` - Mandatory field specifying which Move2Kube internal implementation to use for this transformer. Examples are: `Kubernetes`, `Parameterizer`, `GolangDockerfileGenerator`, `Executable`, `Starlark`, etc.
    - `isolated` : `boolean` - If true, the transformer will receive a full unmodified copy of the source directory. By default, transformers do not run in isolation but instead receive a temporary directory containing a copy of the source directory that has already been used by other transformers. If other transformers have created temporary files, all of those files will be visible to the transformer.

    > **Note:** Running in isolation increases the run time of your transformer but makes writing transformers easier because no clean up is necessary after the transformer has finished.

    - `directoryDetect` : `object` - Used to control the directories the transformer runs on during the planning phase.
        - `levels` : `int` - Supported values:
            - `-1`: Runs on the source directory and all sub-directories.
            - `0`: Skips directory detect entirely, does not run on any directories.
            - `1`: Runs on only the source directory, not on any of the sub-directories.
    - `externalFiles` : `object ([string]: string)` - Used to specify files that need to be copied from [outside the context of the transformer](https://github.com/konveyor/move2kube/blob/023467328100472fc3ff36218af85b10573247f3/assets/built-in/transformers/dockerfilegenerator/java/maven/transformer.yaml#L22-L24) into the transformer. This is helpful to specify files used by multiple transformers in a single location.
    - `consumes` : `object ([string]: object)` - Used to narrow down the artifacts that the transformer runs on during the transformation phase. The key is a string containing the type of the artifact and the value is an object with the following fields:
        - `merge` : `boolean` - If true, all artifacts of this type will be merged into a single artifact before being passed to the transformer.
    - `produces` : `object ([string]: object)` - Used to tell Move2Kube the type of output artifacts the transformer will return. The key is a string containing the type of the artifact and the value is an object with the following fields:
        - `changeTypeTo` : `string` - Used to change the artifact type to something else. Useful for overriding the behavior of existing transformers.
    - `dependency` : `any` - If the transformer wants the artifacts that are about to be processed by this transformer to be [preprocessed](https://github.com/konveyor/move2kube/blob/023467328100472fc3ff36218af85b10573247f3/assets/built-in/transformers/kubernetes/kubernetes/transformer.yaml#L17-L19) by another transformer, this field specifies the transformer to use for preprocessing.
    - `override` : `any` - If this transformer overrides the behavior of other transformers, a [selector](https://github.com/konveyor/move2kube-transformers/blob/25db4c2e46bf9795998ba560fa8fb59d72fcd1a8/custom-helm-kustomize-octemplates-parameterization/transformer.yaml#L14-L16) can be specified to disable those transformers.
    - `templates` : `string` - Specifies the template directory. The default value is `templates`
    - `config` : `any` - Each transformer has a type/class specified by the `class` field which provides certain configuration options that can be configured here. For more details, refer to documentation for the transformer class being used. Example: [Parameterizer config](https://github.com/konveyor/move2kube/blob/023467328100472fc3ff36218af85b10573247f3/assets/built-in/transformers/kubernetes/parameterizer/transformer.yaml#L19-L18)

    #### Other files/directories
    `templates` - If the `Template` type path mapping created by this transformer uses a relative path, it is considered to be relative to this directory. There can be other files/configs in the directory that are interpreted differently by each transformer class which then determines how the values are interpreted and executed.

    ### Transformer Class
    The `Transformer Class` determines the code used for the internal execution of the transformer using the configuration in the `Transformer Yaml` and other config files to model its behavior. There are many transformer classes supported by Move2Kube: `Kubernetes`, `Parameterizer`, `GolangDockerfileGenerator`, `Executable`, `Starlark`, `Router`, etc. Most of them have a specific task, but some transformer classes like `Executable` and `Starlark` are customizable allowing users to write the entire logic of the transformer in the customization.

    #### Transformer Class Internal Implementation
    [Source code](https://github.com/konveyor/move2kube/blob/dcf8793a889c0a8f9f4423e9e9ee3a95003c6bcc/transformer/transformer.go#L53-L60)

    The transformer interface defines the transformer that modifies and converts files to IR representation.
    ```
    type Transformer interface {
        Init(tc transformertypes.Transformer, env *environment.Environment) (err error)
        // GetConfig returns the transformer config
        GetConfig() (transformertypes.Transformer, *environment.Environment)
        DirectoryDetect(dir string) (services map[string][]transformertypes.Artifact, err error)
        Transform(newArtifacts []transformertypes.Artifact, alreadySeenArtifacts []transformertypes.Artifact) ([]transformertypes.PathMapping, []transformertypes.Artifact, error)
    }
    ```
    This example is the interface all transformers are expected to implement.

    - `Transform`: The main function that needs to be implemented.
    - Use `DirectoryDetect` for custom behavior during the planning phase.

    > **Important:** Set `directoryDetect` to a value other than `0` in the transformer YAML.

    - The `Init` and `GetConfig` functions are fixed and implemented by transformers built into Move2Kube. They cannot be implemented by custom transformers.

    ### Methods
    - `Init` : `(Transformer, Environment) -> (error)` - TODO
    - `GetConfig` : `(Transformer, Environment) -> ()` - TODO
    - `DirectoryDetect` : `(string) -> (object ([string]: []Artifact), error)`: This function is called during the planning phase and given the path of a directory containing the source files and then returns a list of artifacts listed in the plan-file generated by Move2Kube. It will also return an error if planning does not run correctly.

        - Input is a string containing the path to a directory with source files which could be the source directory itself or a sub-directory based on the value of `directoryDetect` in the transformer YAML.
            - `-1`: The function runs on the source directory and all of its sub-directories.
            - `0`: The function is disabled entirely (it will not be run on any directories).
            - `1`: The function runs only on the source directory but not on its sub-directories.
        - The output is a list of artifacts which will be included in the plan-file.
    - `Transform` : `([]Artifact, []Artifact) -> ([]PathMapping, []Artifact, error)`: This function is called during the transformation phase and contains the code to perform the actual transformation and produce part of the Move2Kube output. The path mappings returned by this function cause changes to the Move2Kube output and the artifacts returned are passed to other transformers during the next iteration. It will also return an error if planning does not run correctly.
        - The first input is a list of new artifacts produced during the previous iteration.
        - The second input is a list of artifacts that the transformer has already seen.
        - The third output is a list of path mappings.

    ## Path Mapping
    Path mappings are a way for transformers to add files to the Move2Kube output directory. They can be used to generate new files, delete existing files, modify the output directory structure, etc. Usually transformers deal with artifacts as they take them as input and outputs new artifacts, but does nothing to change the Move2Kube output since all transformers are run inside temporary directories.

    In order to affect the output directory, transformers need to return path mappings indicating the type of change to be made.

    For example: Consider a transformer that adds an annotation to Kubernetes Ingress YAML files. The transformer reads the file, adds the annotation, and then writes it back out. However this modified file is only present inside the temporary directory and does not appear in the output directory of Move2Kube. To copy this file over to the output directory, create a path mapping to return this from the transformer.:
    ```json
    {
        "type": "Source",
        "sourcePath": "annotated-ingress.yaml",
        "destinationPath": "deploy/yamls/ingress.yaml"
    }
    ```
    Once the transformer is finished, Move2Kube will look at the path mapping the transformer returned and copy over the file to the output directory.

    The example above shows the simplest use case for path mappings, but they are capable of much more advanced uses. For example: the source file is a template and needs to be filled in before being copied to the output.

    Another example is when the source and destination paths are template strings that need to be filled in order to get the actual paths.

    ### Different types of path mappings
    [Source code](https://github.com/konveyor/move2kube/blob/dcf8793a889c0a8f9f4423e9e9ee3a95003c6bcc/types/transformer/pathmapping.go#L19-L45)

    `PathMappingType` refers to the Path Mapping type.
    ```
    type PathMappingType = string

    const (
        // DefaultPathMappingType allows normal copy with overwrite
        // TemplatePathMappingType allows copy of source to destination and applying of template
        TemplatePathMappingType PathMappingType = "Template" // Source path when relative, is relative to yaml file location
        // SourcePathMappingType allows for copying of source directory to another directory
        SourcePathMappingType PathMappingType = "Source" // Source path becomes relative to source directory
        // DeletePathMappingType allows for deleting of files or directories
        DeletePathMappingType PathMappingType = "Delete" // Delete path becomes relative to source directory
        // ModifiedSourcePathMappingType allows for copying of deltas wrt source
        ModifiedSourcePathMappingType PathMappingType = "SourceDiff" // Source path becomes relative to source directory
        // PathTemplatePathMappingType allows for path template registration
        PathTemplatePathMappingType PathMappingType = "PathTemplate" // Path Template type
        // SpecialTemplatePathMappingType allows copy of source to destination and applying of template with custom delimiter
        SpecialTemplatePathMappingType PathMappingType = "SpecialTemplate" // Source path when relative, is relative to yaml file location
    )
    ```
    PathMapping is the mapping between source and intermediate files and output files.
    ```
    type PathMapping struct {
        Type           PathMappingType `yaml:"type,omitempty" json:"type,omitempty"` // Default - Normal copy
        SrcPath        string          `yaml:"sourcePath" json:"sourcePath" m2kpath:"normal"`
        DestPath       string          `yaml:"destinationPath" json:"destinationPath" m2kpath:"normal"` // Relative to output directory
        TemplateConfig interface{}     `yaml:"templateConfig" json:"templateConfig"`
    }
    ```

    There are seven different types of path mappings:

    - `Default` - `sourcePath` must be an absolute path.
    `destinationPath` must be a relative path, relative to Move2Kube's output directory. This will copy the directory/file specified in `sourcePath` to `destinationPath`.
    - `Template` - `sourcePath` must be a relative path, relative to the templates directory of the transformer.
    `destinationPath` must be a relative path, relative to Move2Kube's output directory. This fills the template in the file given by `sourcePath` and copies the filled template to `destinationPath`. The values for filling the template are given in `templateConfig`.
    - `Source` - Same as `Default` path mapping except now the `sourcePath` can now be a relative path,     relative to the temporary directory where the transformer is running.
    - `Delete` - `sourcePath` must be a relative path, relative to Move2Kube's output directory. The directory/file specified by `sourcePath` will be deleted.
    - `SourceDiff` - TODO
    - `PathTemplate` - The path itself becomes a template. `sourcePath` contains the template path. `templateConfig` can be used to set a name for this template path.
    - `SpecialTemplate` - Same as `Template` path mapping except now the template has a different syntax. The delimiters used in normal templates are `{{` and `}}`. In special templates, the delimiters are `<~` and `~>`. Same as before, the values for filling the template are provided in `templateConfig`.


    ## Phases
    Move2Kube uses two key phases:
    * Planning
    * Transformation

    ### Planning phase
    This phase starts by running the `move2kube plan -s path/to/source/directory` command. Move2Kube runs all the transformers that support the detect capability on the source directory to create a plan. The plan is written to a file called `m2k.plan` in YAML format which the transformation phase will use this plan to modify the source files into the desired output. The plan file is human readable and can be edited manually to change the modifications performed during the transformation phase.

    The plan contains the list of detected services that Move2Kube found inside the source directory, including the path to the sub-directories/files where it detected information about those services. It also contains a list of all the built-in and external transformers that were detected which will be run during the transformation phase. Custom transformers can be written and provided during the plan phase to affect the contents of the plan.

    ### Transformation phase
    This phase starts by running the `move2kube transform` command. Move2Kube evaluates which transformers to run in an iterative manner. Each iteration will evaluate the list of artifacts produced during the previous iteration and runs all transformers that consume those artifact types. This continues until it hits an iteration where there are no more artifacts or transformers that consume those artifact types at which point the transformation phase is complete.

    The evaluated result of all PathMappings is the output.
