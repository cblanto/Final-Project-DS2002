```python
import asyncio
import nest_asyncio
import requests
import pymongo
from datetime import datetime, timedelta

nest_asyncio.apply()

async def fetch_data():
    api_url = 'https://4feaquhyai.execute-api.us-east-1.amazonaws.com/api/pi'
    response = requests.get(api_url)
    if response.status_code == 200:
        return response.json()
    else:
        return None

async def insert_data_mongodb(data):
    client = pymongo.MongoClient("mongodb://localhost:27017/")
    db = client["data_db"]
    collection = db["api_data"]
    collection.insert_one(data)
    client.close()

async def execute_for_one_hour():
    end_time = datetime.now() + timedelta(minutes=60)
    interval_minutes = 1

    while datetime.now() < end_time:
        print(f"Next execution in {interval_minutes} minute(s)...")
        await asyncio.sleep(60 * interval_minutes)
        print("Executing the code...")
        
        data = await fetch_data()
        if data:
            data['timestamp'] = datetime.now()
            await insert_data_mongodb(data)
            print(f"Data retrieved and stored at {datetime.now()}")
        else:
            print("Failed to fetch data from the API.")

if __name__ == "__main__":
    asyncio.run(asyncio.create_task(execute_for_one_hour()))
```

    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:20:26.371655
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:21:26.475091
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:22:26.586230
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:23:26.700826
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:24:26.809294
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:25:26.916944
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:26:27.002459
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:27:27.125085
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:28:27.312821
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:29:27.417072
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:30:27.536775
    Next execution in 1 minute(s)...
    Executing the code...
    Data retrieved and stored at 2023-12-11 22:31:27.659482
    Next execution in 1 minute(s)...
    


    ---------------------------------------------------------------------------

    KeyboardInterrupt                         Traceback (most recent call last)

    Cell In[1], line 42
         39             print("Failed to fetch data from the API.")
         41 if __name__ == "__main__":
    ---> 42     asyncio.run(asyncio.create_task(execute_for_one_hour()))
    

    File C:\Miniconda\envs\py38_default\lib\site-packages\nest_asyncio.py:35, in _patch_asyncio.<locals>.run(main, debug)
         33 task = asyncio.ensure_future(main)
         34 try:
    ---> 35     return loop.run_until_complete(task)
         36 finally:
         37     if not task.done():
    

    File C:\Miniconda\envs\py38_default\lib\site-packages\nest_asyncio.py:84, in _patch_loop.<locals>.run_until_complete(self, future)
         82     f._log_destroy_pending = False
         83 while not f.done():
    ---> 84     self._run_once()
         85     if self._stopping:
         86         break
    

    File C:\Miniconda\envs\py38_default\lib\site-packages\nest_asyncio.py:107, in _patch_loop.<locals>._run_once(self)
        100     heappop(scheduled)
        102 timeout = (
        103     0 if ready or self._stopping
        104     else min(max(
        105         scheduled[0]._when - self.time(), 0), 86400) if scheduled
        106     else None)
    --> 107 event_list = self._selector.select(timeout)
        108 self._process_events(event_list)
        110 end_time = self.time() + self._clock_resolution
    

    File C:\Miniconda\envs\py38_default\lib\selectors.py:323, in SelectSelector.select(self, timeout)
        321 ready = []
        322 try:
    --> 323     r, w, _ = self._select(self._readers, self._writers, [], timeout)
        324 except InterruptedError:
        325     return ready
    

    File C:\Miniconda\envs\py38_default\lib\selectors.py:314, in SelectSelector._select(self, r, w, _, timeout)
        313 def _select(self, r, w, _, timeout=None):
    --> 314     r, w, x = select.select(r, w, w, timeout)
        315     return r, w + x, []
    

    KeyboardInterrupt: 



```python
import pymongo
from pprint import pprint

def retrieve_data():
    client = pymongo.MongoClient("mongodb://localhost:27017/")
    db = client["data_db"]
    collection = db["api_data"]
    data = list(collection.find({})) 
    client.close()
    return data

stored_data = retrieve_data()

print("First 5 records:")
pprint(stored_data[:5])
```

    First 5 records:
    [{'_id': ObjectId('657342ececb133ad87a6550a'),
      'factor': 12167,
      'pi': 3.141674843118693,
      'time': '2023-12-08 16:23:08',
      'timestamp': datetime.datetime(2023, 12, 8, 16, 23, 8, 274000)},
     {'_id': ObjectId('65734328ecb133ad87a6550c'),
      'factor': 13824,
      'pi': 3.141520315626915,
      'time': '2023-12-08 16:24:08',
      'timestamp': datetime.datetime(2023, 12, 8, 16, 24, 8, 715000)},
     {'_id': ObjectId('65734364ecb133ad87a6550e'),
      'factor': 15625,
      'pi': 3.141656653589722,
      'time': '2023-12-08 16:25:08',
      'timestamp': datetime.datetime(2023, 12, 8, 16, 25, 8, 873000)},
     {'_id': ObjectId('657343a1ecb133ad87a65510'),
      'factor': 17576,
      'pi': 3.1415357578228837,
      'time': '2023-12-08 16:26:09',
      'timestamp': datetime.datetime(2023, 12, 8, 16, 26, 9, 58000)},
     {'_id': ObjectId('657343ddecb133ad87a65512'),
      'factor': 19683,
      'pi': 3.1416434588531876,
      'time': '2023-12-08 16:27:09',
      'timestamp': datetime.datetime(2023, 12, 8, 16, 27, 9, 231000)}]
    


```python
# Analysis of Code

# The goal for this data was to get data once per minute over an hour long period from the API source. 
# The basic process for my code revolved around retreiving the data from the API source, storing it in a Mongo DB and
# adjusting the code to allow it to run once per minute for an hour.  The package asyncio was effectively used to complete this
# project as it was able to retieve the data without messing up the existing loop.  As I printed only the first data points,
# it is difficult to see a pattern in the data, each time the factor is increasing, pi is staying the same, and the time stamp 
# increases every minute as expected.  API data like this is important to observe over time in order to observe patterns
# that can be due to a variety of factors.  If you observe more data points, there is an increase in factor, but also variation in
# the pi variable indicating that there might be a relationship.
```
