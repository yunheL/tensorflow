#COMPRESS-TENSORFLOW

##Overview  
Compress-tensorflow added interface for easy compressing training messages using Gzip/deflate.

##Compile
1. Clone code to your local repository.  
2. Please follow the **build from source instructions** from [tensorflow documentation](https://www.tensorflow.org/install/install_sources). 

##Usage
1. **Import config_pb2 in your code.** Protobuf is used to serializing data for tensorflow communication.  
Add this line to the beginning of your training python file to import:

	```python
		from tensorflow.core.protobuf import config_pb2
	```
	
2. **Creating Server.** Each time you create a server using [tf.train.Server(...)](https://www.tensorflow.org/api_docs/python/tf/train/Server), use
 
	```python
		tf.ConfigProto(rpc_options=config_pb2.RPCOptions(ex_grpc_compression=FLAGS.compression_on))
	```

	as the parameter for the **config** field. 

	For example:
	
	```python
	  server = tf.train.Server(cluster,
                           job_name=FLAGS.job_name,
                           task_index=FLAGS.task_index,
        			       config=tf.ConfigProto(rpc_options=config_pb2.RPCOptions(ex_grpc_compression=FLAGS.compression_on))
                          )
   ```

3. **Creating session.** Each time you create a session using [tf.train.MonitoredTrainingSession(...)](https://www.tensorflow.org/api_docs/python/tf/train/MonitoredTrainingSession) or other methods that create sessions, use

	```python
		tf.ConfigProto(rpc_options=config_pb2.RPCOptions(ex_grpc_compression=FLAGS.compression_on))
	```
	
	as the parameter for the **config** field.
	
	For example:
	
	```python
	tf.train.MonitoredTrainingSession(master=server.target,
                                           is_chief=(FLAGS.task_index == 0),
                                           checkpoint_dir="/tmp/train_logs",
                                           config=tf.ConfigProto(rpc_options=config_pb2.RPCOptions(ex_grpc_compression=FLAGS.compression_on)),
                                           hooks=hooks)
	```

4. **If you would like to use command line argument to switch on/off compression.** Here is an example of how to do it.

	```python
	import argparse
	
	parser = argparse.ArgumentParser()
	
  	parser.add_argument(  
      	"--compression_on",  
      	type=str2bool,  
      	default=True,  
      	help="whether to turn on compression"
  	)  
  	FLAGS, unparsed = parser.parse_known_args()
	```

	Add **--compression_on=True when running python files.** Then you will be good to go :)  
	
	```python
	python *FILENAME* --compression_on=True
	```
	
##Contact
Please contact Xiangjin <xwu@cs.wisc.edu>, Yunhe <liu348@wisc.edu> if you have any questions or concerns. Thank you!


