#### 什么是redis持久化

Redis持久化是指将Redis服务器中的数据保存到磁盘中，以确保在服务器停机或崩溃时不会丢失数据。Redis支持两种持久化方式：

- RDB（Redis DataBase）：将Redis在内存中的数据快照定期写入磁盘中，形成RDB文件。
- AOF（Append Only File）：将Redis的所有写操作（包括修改、删除等）追加到AOF文件中，以保证数据的持久性。

#### 为什么要redis持久化

Redis是一种内存型数据库，数据存储在内存中，每个键值对都被缓存在RAM中。虽然这种方式可以提供快速、高效的数据读写能力，但是在发生服务器停机、崩溃或断电等异常情况时，所有数据也会在瞬间丢失，这对于一些需要持久存储的业务场景来说是无法接受的。因此，Redis提供了持久化功能，以确保即使发生异常情况，数据也能被恢复，保护数据的完整性和可靠性。

#### redis默认的持久化配置是什么

Redis默认的持久化配置是不开启任何持久化方式，即默认情况下Redis服务器不会将数据持久化到磁盘中。这意味着，如果Redis服务器在运行过程中发生异常，所有数据都会丢失。如果需要开启持久化功能，需要在配置文件redis.conf中进行相应的配置。具体配置方式如下：

- RDB持久化配置



```bash
# 开启RDB持久化
save 900 1
save 300 10
save 60 10000
# 指定RDB快照文件的名称和存储位置
dbfilename dump.rdb
dir /usr/local/redis/data
```

Copy

- AOF持久化配置：



```bash
# 开启AOF持久化
appendonly yes
# 指定AOF文件的名称和存储位置
appendfilename "appendonly.aof"
dir /usr/local/redis/data
```

Copy

注意：配置文件中的持久化参数配置顺序很重要，如果不按照正确的顺序，可能会导致持久化功能无法正常工作。

#### 如何选择持久化，可以两个同时开启吗

可以同时开启RDB和AOF两种持久化方式，以确保数据的最大可靠性。同时开启两种持久化方式可以提供更高的数据安全性和可靠性。如果需要同时开启两种持久化方式，在配置文件redis.conf中进行如下配置：



```bash
# 同时开启RDB和AOF持久化
save 900 1
save 300 10
save 60 10000
appendonly yes
dbfilename dump.rdb
dir /usr/local/redis/data
appendfilename "appendonly.aof"
```

Copy

需要注意的是，开启两种持久化方式会增加Redis服务器的IO负载和磁盘空间占用，因此在选择持久化方式时需要根据实际业务需要进行选择和配置。

#### 如果redis崩溃了，如何通过这两种持久化恢复数据

Redis提供了两种持久化方式，即RDB和AOF，如果Redis服务器崩溃或停机，可以通过以下方式进行数据恢复：

##### RDB持久化恢复：

如果开启了RDB持久化，在Redis服务器崩溃后，可以通过将RDB文件加载到Redis服务器中来恢复数据。Redis在启动时会自动检查是否存在RDB文件，如果存在，则会将RDB文件中的数据加载到内存中。可以通过以下命令来手动加载RDB文件：

redis-cli -h -p --rdb </path/to/dump.rdb>

##### AOF持久化恢复：

如果开启了AOF持久化，在Redis服务器崩溃后，可以通过重放AOF文件中的命令操作来恢复数据。Redis在启动时会自动加载最新的AOF文件，并将其中的写操作逐个执行，恢复数据到内存中。可以通过以下命令来手动重放AOF文件：

redis-cli -h -p --appendonly yes 需要注意的是，在进行数据恢复之前，需要先确保Redis配置文件中的相关参数正确，并且保存了正确的RDB文件和AOF文件。

#### 我该如何选择RDB还是AOF呢

选择 RDB 还是 AOF，需要考虑以下几个方面：

- 数据安全性要求：如果数据安全性要求比较高，建议选择 AOF。因为 AOF 可以记录每一个写操作的命令，可以确保每次写操作都会被持久化下来。
- 数据的重要性：如果数据的重要性比较高，建议同时使用 RDB 和 AOF 进行备份，以确保数据的最大可靠性。这两种持久化方式可以结合使用，RDB 配合 AOF 一起使用，可以最大限度地保证数据的可靠性。
- 数据的更新频率：如果数据的更新频率很高，AOF 的性能可能会受到一定的影响，因为每次写操作都会被记录下来。如果数据的更新频率不是很高，建议选择 AOF，以获得更好的数据安全性。
- 备份和恢复时间：RDB 可以实现比 AOF 更快速的备份和恢复。如果备份和恢复时间比较关键，可以选择 RDB 方式。

根据以上几个方面的考虑，您可以选择适合自己场景的持久化方式。在实际应用中，大多数情况下建议同时开启 RDB 和 AOF 持久化方式，以达到更高的数据安全性和可靠性。

#### RDB是如何实现的

RDB 是 Redis 的一种持久化方式，可以将 Redis 内存中的数据转储到磁盘上，以防止进程退出后数据的丢失。 RDB 的实现机制是，Redis 定期将内存中的数据快照写入磁盘中的 RDB 文件中。当 Redis 需要进行数据恢复时，只需要加载 RDB 文件即可。RDB 文件的格式是 Redis 自己定义的，可以通过配置文件来指定 RDB 文件的生成周期。

在 RDB 中，Redis 会将内存中的数据进行压缩和简化，以减小 RDB 文件的大小。为此，Redis 会在生成 RDB 文件时，暂停对客户端的服务，以便将 Redis 内存中的数据写入 RDB 文件中。在此期间，Redis 将会阻塞所有的客户端请求，直到 RDB 文件的生成过程完成。

RDB 的优点是，它可以在 Redis 发生故障时提供一定程度的数据保护。 RDB 文件通常比 AOF 文件更小，因此恢复速度更快。此外，由于 RDB 可以更频繁地进行备份，因此在某些情况下，使用 RDB 可以提供更好的数据保护。

需要注意的是，由于 Redis 在生成 RDB 文件时需要暂停对客户端的服务，因此在使用 RDB 时需要权衡备份频率和 Redis 的性能。如果备份频率太高，可能会影响 Redis 的性能。

#### AOF是如何实现的

AOF（Append Only File）是 Redis 的一种持久化方式，可以将每个修改 Redis 数据库的命令记录下来，以便在 Redis 重启时重新执行这些命令，从而恢复数据。

AOF 的实现机制是，每个写操作都会被记录到 AOF 文件中，Redis 会将每个写操作转化为一个文本格式的命令，然后将其追加到 AOF 文件的末尾。当 Redis 重启时，会将 AOF 文件中保存的命令重新执行一遍，从而恢复数据。

AOF 文件通常会以多种方式进行同步，以确保数据的可靠性和一致性。例如：

1. always：每个写操作都会立即同步到磁盘。
2. everysec：每秒钟同步一次 AOF 文件。
3. no：不进行同步。

需要注意的是，AOF 文件比 RDB 文件更大，因为它记录了每个写操作。此外，AOF 备份和恢复的速度通常比 RDB 慢，因为 Redis 需要逐行读取和执行 AOF 文件中的每个命令。

虽然 AOF 文件比 RDB 文件更大，备份和恢复速度也比较慢，但是 AOF 在数据可靠性和一致性方面更具优势，因为它可以保证每个写操作都被记录下来，从而最大限度地避免数据丢失。

#### 如何手动进行备份

手动备份 Redis 数据通常可以通过以下两种方式进行：

##### 使用 SAVE 命令手动备份

save: 该命令会阻塞当前redis服务器，执行save命令期间，redis不能处理其他命令，直到RDB过程结束为止(会造成长时间阻塞，不建议使用) bgsave:该命令执行后，redis会在后台异步进行快照操作，快照同时还可以响应客户端的请求，阻塞只发生在fork阶段，基本上redis内部的所有RDB操作都是采用bgsave命令



```bash
#save
redis-cli -h <host> -p <port> save
#bgsave
redis-cli -h <host> -p <port> bgsave
```

Copy

该命令将会在 Redis 默认的工作目录中生成一个名为 dump.rdb 的备份文件，其中包含当前 Redis 服务器上的所有数据。您可以将备份文件移动到安全位置，以进行数据恢复。

##### 复制 RDB 和 AOF 文件手动备份

另一种手动备份 Redis 数据的方式是直接复制 RDB 和 AOF 文件。使用以下命令：



```bash
cp /var/lib/redis/dump.rdb /path/to/backup/dir
cp /var/lib/redis/appendonly.aof /path/to/backup/dir
```

Copy

这将复制 Redis 的 RDB 文件和 AOF 文件到指定的备份目录中，以进行数据恢复。需要注意的是，如果同时使用 RDB 和 AOF 持久化方式，建议同时备份 RDB 文件和 AOF 文件，以便在数据恢复时可以选择最合适的备份方式。

#### 什么是AOF的重写机制

Redis 的 AOF 文件在不断地记录写命令的操作，随着时间推移，AOF 文件会越来越大。数据量太大会导致 AOF 文件加载和恢复时变得缓慢。为了解决这个问题，Redis 引入了 AOF 重写机制。

AOF 重写是一种将 AOF 文件进行裁剪和压缩的机制，其目的是减小 AOF 文件的大小，从而提高 Redis 数据库的性能。AOF 重写机制在后台运行，它会生成一个新的 AOF 文件，这个新的 AOF 文件包含了 Redis 数据库中的所有写命令。当新的 AOF 文件生成完毕时，Redis 会用新的 AOF 文件覆盖旧的 AOF 文件。

AOF 重写机制的实现是基于 Redis 数据库的内部数据结构。重写过程中，Redis 会遍历内部数据结构，将其中的写命令重新生成到新的 AOF 文件中。在 AOF 重写的过程中，Redis 会尽可能地采用批量操作的方式，以减少重写过程中的内存占用和磁盘 I/O 操作。

需要注意的是，AOF 重写机制是通过开启一个新的子进程来实现的，因此在 AOF 重写期间，Redis 的主进程仍然可以往 AOF 文件中写入新的命令，这些新命令不会写入到重写后的 AOF 文件中。AOF 重写的时间间隔可以通过配置文件中的 auto-aof-rewrite-min-size 和 auto-aof-rewrite-percentage 参数来进行设置。

#### 那重写期间有新的命令进来怎么办

在 AOF 重写期间有新的命令进来，Redis 仍然会将这些新的命令写入 AOF 文件中。这些新的命令不会被 AOF 重写过程覆盖掉，因为 AOF 重写是在 Redis 启动一个子进程来进行的，不会影响 Redis 主进程的写操作。

当 AOF 重写完成后，Redis 会将新的命令写入到新的 AOF 文件中，而不是原来的 AOF 文件中。这意味着，新的 AOF 文件会包含 Redis 数据库中的所有写命令，包括在 AOF 重写期间写入的新命令。

需要注意的是，如果在 AOF 重写期间有新的命令写入到 AOF 文件中，可能会导致新的 AOF 文件比旧的 AOF 文件更大。因此，建议在进行 AOF 重写之前，先停止对 Redis 数据库的写操作，以确保 AOF 文件能够尽可能地被压缩和裁剪。

#### RDB的优缺点有哪些

##### 优点：

1. RDB 可以实现 Redis 数据库的快速备份。因为 RDB 文件只记录 Redis 数据库在某一个时间点的状态，所以在数据库出现问题时，使用 RDB 文件进行数据恢复可以更快速。
2. RDB 文件通常比 AOF 文件更小，因为 RDB 只记录 Redis 数据库在某一个时间点的状态，而 AOF 则记录了每个写命令。因此，如果您的 Redis 数据库经常进行大量写操作，使用 RDB 可以减少磁盘 I/O 和网络带宽的使用。
3. RDB 持久化方式可以单独使用，而 AOF 持久化方式必须和 RDB 持久化方式一起使用。如果您对数据的实时性要求不高，或者您的 Redis 数据库是只读的，那么使用 RDB 持久化方式就足够了。

##### 缺点：

1. RDB 只能实现定期备份，不能实现实时备份。如果您对 Redis 数据库的实时性要求较高，使用 RDB 可能会导致一定程度的数据丢失。
2. RDB 文件只记录 Redis 数据库在某一个时间点的状态，如果在备份间隔内出现问题，可能会导致数据丢失。
3. RDB 文件的生成过程需要占用一定的内存和 CPU 资源，可能会影响 Redis 的性能。因此，备份的频率需要仔细考虑，以权衡备份频率和 Redis 的性能。

#### AOF的优缺点有哪些

##### 优点：

1. AOF 可以实现实时备份。因为 AOF 文件记录了每个写命令，所以即使 Redis 数据库在备份间隔内出现问题，也可以尽量避免数据丢失。
2. AOF 文件是一个文本文件，易于维护和备份。与 RDB 文件相比，AOF 文件更加灵活，可以随时进行备份和恢复。
3. AOF 文件记录了 Redis 数据库的历史修改记录，可以在 Redis 数据库出现问题时进行数据恢复。同时，AOF 文件也可以用于 Redis 数据库的主从复制，以及 Redis Sentinel 的故障转移。

##### 缺点：

1. AOF 文件通常比 RDB 文件更大，因为 AOF 文件记录了每个写操作。这可能会导致 AOF 文件的加载和恢复变得缓慢。
2. AOF 备份和恢复的速度通常比 RDB 慢，因为 Redis 需要逐行读取和执行 AOF 文件中的每个命令。
3. AOF 持久化方式需要额外的磁盘 I/O 和网络带宽，因为要将每个写操作写入磁盘文件，这可能会对 Redis 的性能产生一定的影响。
4. AOF 持久化方式必须和 RDB 持久化方式一起使用，以尽可能地避免数据丢失。这使得 AOF 持久化方式的配置和管理变得更加复杂。

### 配置文件查看

如下是/etc/redis.conf 关于持久化的配置



```bash
################################ SNAPSHOTTING  ################################
#
# Save the DB on disk:
#
#   save <seconds> <changes>
#
#   Will save the DB if both the given number of seconds and the given
#   number of write operations against the DB occurred.
#
#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
#
#   Note: you can disable saving completely by commenting out all "save" lines.
#
#   It is also possible to remove all the previously configured save
#   points by adding a save directive with a single empty string argument
#   like in the following example:
#
#   save ""

save 900 1
save 300 10
save 60 10000

# By default Redis will stop accepting writes if RDB snapshots are enabled
# (at least one save point) and the latest background save failed.
# This will make the user aware (in a hard way) that data is not persisting
# on disk properly, otherwise chances are that no one will notice and some
# disaster will happen.
#
# If the background saving process will start working again Redis will
# automatically allow writes again.
#
# However if you have setup your proper monitoring of the Redis server
# and persistence, you may want to disable this feature so that Redis will
# continue to work as usual even if there are problems with disk,
# permissions, and so forth.
stop-writes-on-bgsave-error yes

# Compress string objects using LZF when dump .rdb databases?
# For default that's set to 'yes' as it's almost always a win.
# If you want to save some CPU in the saving child set it to 'no' but
# the dataset will likely be bigger if you have compressible values or keys.
rdbcompression yes

# Since version 5 of RDB a CRC64 checksum is placed at the end of the file.
# This makes the format more resistant to corruption but there is a performance
# hit to pay (around 10%) when saving and loading RDB files, so you can disable it
# for maximum performances.
#
# RDB files created with checksum disabled have a checksum of zero that will
# tell the loading code to skip the check.
rdbchecksum yes

# The filename where to dump the DB
dbfilename dump.rdb

# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
#
# Note that you must specify a directory here, not a file name.
dir /var/lib/redis







############################## APPEND ONLY MODE ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check http://redis.io/topics/persistence for more information.

appendonly no

# The name of the append only file (default: "appendonly.aof")

appendfilename "appendonly.aof"

# The fsync() call tells the Operating System to actually write data on disk
# instead of waiting for more data in the output buffer. Some OS will really flush
# data on disk, some other OS will just try to do it ASAP.
#
# Redis supports three different modes:
#
# no: don't fsync, just let the OS flush the data when it wants. Faster.
# always: fsync after every write to the append only log. Slow, Safest.
# everysec: fsync only one time every second. Compromise.
#
# The default is "everysec", as that's usually the right compromise between
# speed and data safety. It's up to you to understand if you can relax this to
# "no" that will let the operating system flush the output buffer when
# it wants, for better performances (but if you can live with the idea of
# some data loss consider the default persistence mode that's snapshotting),
# or on the contrary, use "always" that's very slow but a bit safer than
# everysec.
#
# More details please check the following article:
# http://antirez.com/post/redis-persistence-demystified.html
#
# If unsure, use "everysec".

# appendfsync always
appendfsync everysec
# appendfsync no

# When the AOF fsync policy is set to always or everysec, and a background
# saving process (a background save or AOF log background rewriting) is
# performing a lot of I/O against the disk, in some Linux configurations
# Redis may block too long on the fsync() call. Note that there is no fix for
# this currently, as even performing fsync in a different thread will block
# our synchronous write(2) call.
#
# In order to mitigate this problem it's possible to use the following option
# that will prevent fsync() from being called in the main process while a
# BGSAVE or BGREWRITEAOF is in progress.
#
# This means that while another child is saving, the durability of Redis is
# the same as "appendfsync none". In practical terms, this means that it is
# possible to lose up to 30 seconds of log in the worst scenario (with the
# default Linux settings).
#
# If you have latency problems turn this to "yes". Otherwise leave it as
# "no" that is the safest pick from the point of view of durability.

no-appendfsync-on-rewrite no

# Automatic rewrite of the append only file.
# Redis is able to automatically rewrite the log file implicitly calling
# BGREWRITEAOF when the AOF log size grows by the specified percentage.
#
# This is how it works: Redis remembers the size of the AOF file after the
# latest rewrite (if no rewrite has happened since the restart, the size of
# the AOF at startup is used).
#
# This base size is compared to the current size. If the current size is
# bigger than the specified percentage, the rewrite is triggered. Also
# you need to specify a minimal size for the AOF file to be rewritten, this
# is useful to avoid rewriting the AOF file even if the percentage increase
# is reached but it is still pretty small.
#
# Specify a percentage of zero in order to disable the automatic AOF
# rewrite feature.

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# An AOF file may be found to be truncated at the end during the Redis
# startup process, when the AOF data gets loaded back into memory.
# This may happen when the system where Redis is running
# crashes, especially when an ext4 filesystem is mounted without the
# data=ordered option (however this can't happen when Redis itself
# crashes or aborts but the operating system still works correctly).
#
# Redis can either exit with an error when this happens, or load as much
# data as possible (the default now) and start if the AOF file is found
# to be truncated at the end. The following option controls this behavior.
#
# If aof-load-truncated is set to yes, a truncated AOF file is loaded and
# the Redis server starts emitting a log to inform the user of the event.
# Otherwise if the option is set to no, the server aborts with an error
# and refuses to start. When the option is set to no, the user requires
# to fix the AOF file using the "redis-check-aof" utility before to restart
# the server.
#
# Note that if the AOF file will be found to be corrupted in the middle
# the server will still exit with an error. This option only applies when
# Redis will try to read more data from the AOF file but not enough bytes
# will be found.
aof-load-truncated yes
```



