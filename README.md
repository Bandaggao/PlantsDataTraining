# PlantsDataTraining

## How to start tranining robot
## Steps
1. Convert image size (This is recommended if your image resolution is too HD)
2. Generate train_labels.csv and test_labels. csv using xml_to_csv.py
3. Modify generate_tfrecord.py 
- Change this part with your desired labels

```
def class_text_to_int(row_label):
    if row_label == 'Aloevera':
        return 1
    elif row_label == 'Insulin':
        return 2
    elif row_label == 'Oregano':
        return 3
    elif row_label == 'Baston de san jose':
        return 4
    elif row_label == 'Pothos plant':
        return 5
    elif row_label == 'Snake plant':
        return 6
    elif row_label == 'Yellow and green leaf':
        return 7
    else:
        return 0
  ```
      
4. generate test.record and traing.record using generate_tfrecord
5. Create labelmap.pbtxt(check ConfigFiles folder for reference)
6. Modify `faster_rcnn_inception_v2_pets.config`
- change this part to number of your classes in my case I have 7
``` 
faster_rcnn {
    num_classes: 7
    image_resizer {
      keep_aspect_ratio_resizer {
        min_dimension: 600
        max_dimension: 1024
      }
    }
```

- change this part to path in your machine
``` 
train_input_reader: {
  tf_record_input_reader {
    input_path: "YOUR_PATH/train.record"
  }
  label_map_path: "YOUR_PATH/labelmap.pbtxt"
}

eval_config: {
  metrics_set: "coco_detection_metrics"
  num_examples: 1101
}

eval_input_reader: {
  tf_record_input_reader {
    input_path: "YOUR_PATH/test.record"
  }
  label_map_path: "YOUR_PATH/labelmap.pbtxt"
  shuffle: false
  num_readers: 1
}
```
7. Train you model by excuting train.py
8. Export inference graph using export_inference_graph.py

# Commands
1. To convert image
- ``python transform_image_resolution -d image_directory_here -s desired_image_size``
- ``example: python transfom_image_resolution -d objectdection/images/ -s 300 300``
2. Generate csv file
- ``python xml_to_csv.py --inputDir=Your_XML_FILE_DIRECTORY_HERE --output=Directory_Where_you_want_CSV_To_be_created\train_labelss.csv``
 - ``example: python xml_to_csv.py --inputDir=C:\object_detection\images\train\ --output=C:\object_detection\images\train_labelss.csv``
3. Generate tfrecord.
- ``python generate_tfrecord.py --csv_input=YOUR_CSV_DIRECTORY_HERE --image_dir=YOUR_IMAGE_DIRECTORY_HERE --output_path=YOUR_OUTPUT_PATH_HERE``
- ``Example: python generate_tfrecord.py --csv_input=C:/images/train_labels.csv --image_dir=C:/images/train/ --output_path=C:/images/train.record``
4. Train model
- ``train.py --logtostderr --train_dir=directory_where_you_want_training_will_be_found -pipeline_config_path=C:\YOUR_DIRECTORY_HERE\faster_rcnn_inception_v2_pets.config``
- ``Example: train.py --logtostderr --train_dir=C:/trainingPlants/ -pipeline_config_path=C:\object_detection\images\faster_rcnn_inception_v2_pets.config``
5. Export inference graph
- ``python export_inference_graph.py --input_type image_tensor --pipeline_config_path C:/YOUR_DIRECTORY_HERE/faster_rcnn_inception_v2_pets.config --trained_checkpoint_prefix C:\YOUR_DIRECTORY_HERE\model.ckpt-LAST_CHECKPOINT_NUMBER --output_directory inference_graph``
- ``Example: python export_inference_graph.py --input_type image_tensor --pipeline_config_path C:/object_detection/images/faster_rcnn_inception_v2_pets.config --trained_checkpoint_prefix C:\trainingPlants\model.ckpt-3485 --output_directory inference_graph``