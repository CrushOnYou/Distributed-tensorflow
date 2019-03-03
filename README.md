# Distributed-tensorflow
how to use distributed tensorflow(easy version)

以下是分布式计算的一般流程：

# 第1步：命令行参数解析，获取集群的信息ps_hosts和worker_hosts，以及当前节点的角色信息job_name和task_index

# 第2步：创建当前task结点的Server
cluster = tf.train.ClusterSpec({"ps": ps_hosts, "worker": worker_hosts})
server = tf.train.Server(cluster, job_name=FLAGS.job_name, task_index=FLAGS.task_index)

# 第3步：如果当前节点是ps，则调用server.join()无休止等待；如果是worker，则执行第4步。
if FLAGS.job_name == "ps":
    server.join()

# 第4步：则构建要训练的模型
# build tensorflow graph model

# 第5步：创建tf.train.Supervisor来管理模型的训练过程
# Create a "supervisor", which oversees the training process.
sv = tf.train.Supervisor(is_chief=(FLAGS.task_index == 0), logdir="/tmp/train_logs")
# The supervisor takes care of session initialization and restoring from a checkpoint.
sess = sv.prepare_or_wait_for_session(server.target)
# Loop until the supervisor shuts down
while not sv.should_stop()
     # train model
