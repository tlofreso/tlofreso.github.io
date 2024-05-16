---
draft: true
date: 2024-02-19
authors:
  - me
---

# Proofreading Blog Posts with ChatGPT

## Problems I ran into
When developing a custom github action, I elected to use Python. This requires that you run a docker container action. Actions are built out of box on javascript. If using something else, you need docker.

I used this for my custom github action dev environment: https://github.com/nektos/act

I hit an error on the first run...

```logs
tlofreso@Anthonys-MacBook-Pro:~/Projects/gpt-proofread$ act
[main.yml/hello] 🚀  Start image=catthehacker/ubuntu:act-latest
INFO[0000] Parallel tasks (0) below minimum, setting to 1 
[main.yml/hello]   🐳  docker pull image=catthehacker/ubuntu:act-latest platform= username= forcePull=true
[main.yml/hello] using DockerAuthConfig authentication for docker pull
INFO[0000] Parallel tasks (0) below minimum, setting to 1 
[main.yml/hello]   🐳  docker create image=catthehacker/ubuntu:act-latest platform= entrypoint=["tail" "-f" "/dev/null"] cmd=[] network="host"
[main.yml/hello]   🐳  docker run image=catthehacker/ubuntu:act-latest platform= entrypoint=["tail" "-f" "/dev/null"] cmd=[] network="host"
[main.yml/hello] ⭐ Run Main Checkou repo
[main.yml/hello]   🐳  docker cp src=/Users/tlofreso/Projects/gpt-proofread/. dst=/Users/tlofreso/Projects/gpt-proofread
[main.yml/hello]   ✅  Success - Main Checkou repo
[main.yml/hello] ⭐ Run Main Check Grammar and Spelling
[main.yml/hello]   🐳  docker build -t act-dockeraction:latest /Users/tlofreso/Projects/gpt-proofread/
[main.yml/hello]   ❌  Failure - Main Check Grammar and Spelling
[main.yml/hello] The command '/bin/sh -c pip install --no-cache-dir -r requirements.txt' returned a non-zero code: 2
[main.yml/hello] 🏁  Job failed
Error: Job 'hello' failed
tlofreso@Anthonys-MacBook-Pro:~/Projects/gpt-proofread$ 
```


Turned on verbose with `act -v` and saw where it was failing:

```logs
# Lots of logs above...
  File "/usr/local/lib/python3.11/site-packages/pip/_internal/operations/prepare.py", line 391, in _fetch_metadata_using_link_data_attr
    metadata_file = get_http_url(
                    ^^^^^^^^^^^^^
  File "/usr/local/lib/python3.11/site-packages/pip/_internal/operations/prepare.py", line 109, in get_http_url
    from_path, content_type = download(link, temp_dir.path)
                              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.11/site-packages/pip/_internal/network/download.py", line 147, in __call__
    for chunk in chunks:
  File "/usr/local/lib/python3.11/site-packages/pip/_internal/cli/progress_bars.py", line 52, in _rich_progress_bar
    with progress:
  File "/usr/local/lib/python3.11/site-packages/pip/_vendor/rich/progress.py", line 1169, in __enter__
    self.start()
  File "/usr/local/lib/python3.11/site-packages/pip/_vendor/rich/progress.py", line 1160, in start
    self.live.start(refresh=True)
  File "/usr/local/lib/python3.11/site-packages/pip/_vendor/rich/live.py", line 132, in start
    self._refresh_thread.start()
  File "/usr/local/lib/python3.11/threading.py", line 964, in start
    _start_new_thread(self._bootstrap, ())
RuntimeError: can't start new thread

[main.yml/hello] [DEBUG] The command '/bin/sh -c pip install --no-cache-dir -r requirements.txt' returned a non-zero code: 2
[main.yml/hello]   ❌  Failure - Main Check Grammar and Spelling
[main.yml/hello] The command '/bin/sh -c pip install --no-cache-dir -r requirements.txt' returned a non-zero code: 2
[main.yml/hello] [DEBUG] skipping post step for 'Checkou repo': no action model available
[main.yml/hello] 🏁  Job failed
[main.yml/hello] [DEBUG] Loading revision from git directory
[main.yml/hello] [DEBUG] Found revision: c185c45cce57c149f612fb771dbde070935b3453
[main.yml/hello] [DEBUG] HEAD points to 'c185c45cce57c149f612fb771dbde070935b3453'
[main.yml/hello] [DEBUG] using github ref: refs/heads/main
[main.yml/hello] [DEBUG] Found revision: c185c45cce57c149f612fb771dbde070935b3453
Error: Job 'hello' failed
tlofreso@Anthonys-MacBook-Pro:~/Projects/gpt-proofread$
```

Looks like it's pip complaining about rich. I happened to find a stack overflow answer that referenced progress bars being a problem here: https://stackoverflow.com/a/77374694


The fix was to change this:
```dockerfile
# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt
```

to this
```dockerfile
# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt --progress-bar off
```