# TensorFlow.js to TensorFlow Lite Model Conversion
This example shows how you can go about converting the model trained with TensorFlowJS to TensorFlow Lite FlatBuffers.

# Deserializing the Keras model from TensorFlowJS
To begin with, we have to convert the sequential block trained with TensorFlow.js to Keras H5. This is done by passing the JSON model configuration and the binary weights files downloaded from TensorFlow.js to the following converter which generates a tf.Keras equivalent H5 model. 
This is achieved by employing tfjs-converter's load_keras_model method which is going to deserialize the Keras objects.

# Merging the base model and the classification block
This step involves loading the aforementioned custom classification block that was just generated and then passing the output of the base model's intermediate Depthwise Convolutional Layer's activation as input to the top classification block. On a side note, one can opt to switch the base MobileNet model with any other pre-trained model which the user thinks fits best. 

```python

...
layer = self.base_model.get_layer('<insert the intermediate layer name here>') # e.g., conv_pw_13_relu
model = Model(inputs=self.base_model.input, outputs=self.top_model(layer.output))
...

```

# Freeze the graph
After obtaining the Keras H5 model, the session is cleared of all the global variables and reloaded again from the saved Keras model. 
Here, the output node(s) is/are named and a prefix is used.

```python

...
sess = K.get_session()

pred = [None] * num_outputs
pred_node_names = [None] * num_outputs

for i in range(num_outputs):
    pred_node_names[i] = output_node_prefix + str(i)
    pred[i] = tf.identity(model.outputs[i], name=pred_node_names[i])
    
constant_graph = graph_util.convert_variables_to_constants(sess, sess.graph.as_graph_def(), pred_node_names)
graph_io.write_graph(constant_graph, '.', frozen_model_file, as_text=False)
...

```

# Generating the TensorFlow Lite model
Finally, the TensorFlow Lite Optimizing Converter or TOCO is used to convert the merged Keras model to TFLite FlatBuffers. Here, the input and output arrays names may be passed to the converter. 

```python
converter = tf.contrib.lite.TocoConverter.from_frozen_graph(
frozen_model_file, input_arrays, output_arrays)
tflite_model = converter.convert()
```
     
All of the above actions can be performed with a single click by running `convert.py`

**Usage**

```bash
python convert.py --config_json_path models/models/model.json \
                    --weights_path_prefix models/models \
                    --frozen_model_file model.pb \
                    --tflite_model_file gesture.tflite
```

| Required Options | Description
|---|---|
|`--config_json_path`     | Path to the TensorFlow.js weights manifest file containing the model architecture (model.json) |
|`--weights_path_prefix`| Optional path prefix for the weights files. If not specified (`None`), will assume the prefix is the same directory as the dirname of `config_json_path` |
|`--tflite_model_file`| The name of the TensorFlow Lite model to be exported. |


| Optional Parameters | Description
|---|---|
|`--use_unique_name_scope`     | Use a unique ID as the name scope for the loaded model. |
|`--alpha`| Controls the width of the network. [0.25, 0.50, 0.75, 1.0] (default: 0.25). |
|`--intermediate_node_name`| Name of the intermediate Depthwise Convolutional Layer. |
|`--image_size`| Size of the input image may be 128, 160, 192 or 224 (default: 224). |
|`--output_node_prefix`| Output node prefix for the Frozen Protocol Buffer Model. |
|`--keras_model_file`| The name of the merged Keras H5 model to be saved. |
|`--frozen_model_file`| The name of the merged TensorFlow Protocol Buffer model to be saved. |

This will export the TensorFlow Lite model ready for inference on a mobile device such as Android or iOS.

# Colab Notebook
A colaboratory notebook is also available **[here](./tensorflowjs_to_tflite_colab_notebook.ipynb)** that can be run directly in Google Colab.

# References
To know more about converting TensorFlow models from a SavedModel, Keras H5, Session Bundle, Frozen Model or Tensorflow Hub module to a web-friendly format and exporting a tfjs model to Keras H5, visit [here](https://github.com/tensorflow/tfjs-converter)