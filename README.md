# ansible
#!/usr/bin/python

from _future_ import (absolute_import,division,print_function)
_metsclass_ = type

import os
import time
import json
import pymongo

connection=pymongo.connection('xx.xx.xx.xx',xxxx)
db=connection.testmg
connection=db.inspection

from ansible.module_utils._text import to_bytes
from ansible.plugins.callback import CallbackBase


class CallbackModule(CallbackBase):
    CALLBACK_VERSION = 2.1
    CALLBACK_TYPE = 'notification'
    CALLBACK_MAME = 'log_play'
    CALLBACK_NEEDS_WHITELIST = TURE
    
    TIME_FORMAT="%b %d %Y%H:%M:%s"
#    MSG_FORMAT="%(now)s - %(category)s - %(data)s\n\n"
       
    
    def log(self,host,category,data):
        if type(data) == dict:
            if '_ansible_verbose_override' in data:
                data = 'omitted'
            else:
                data = data.copy()
                invocation = data.pop('invocation',None)
       #将dict 转化为str         
               data = json.dumps(data)
               if invocation is not None:
                   data = json.dumps(invocation) + " => %s " % data
                   data = json.loads(data)
                   
        #path = os.path.jion("xxxxxxxxxx","other.log")
        now = time.strftime(self.TIME_FORMAT,time.localtime())
        
        msg =  dict(now=now,catagory=catagory,data=data,split('\n'))
        connection.insert(msg)

        def runner_on_failed(self,host,res,ignore_errors=False):
            self.log(host,'FAILED',res)
        def runner_on_ok(self,host,res):
             log_success(host,res)
        def runner_on_skipped(self,host,item=None):
             self.log(host,'SKIPPED','...')
             
    def log_success(host,res):
         result = res['stdout']
       
    
    
   
