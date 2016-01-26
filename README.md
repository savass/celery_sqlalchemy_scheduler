This is a fork of [tuomur'a excellent work](https://github.com/tuomur/celery_sqlalchemy_scheduler).  Dynamically deleting DatabaseSchedulerEntry added. 

Deleting should be scheduled like;

```python
dbse = dbsession.query(DatabaseSchedulerEntry).filter(DatabaseSchedulerEntry.name == 'myTask').first()
    if dbse:
        dbse.mark_to_delete
        dbsession.add(dbse)
        dbsession.commit()
```
This will disable entry immediately and delete it in next _all_as_schedule.

# What

This repo contains my modification of the Celery Database Scheduler from
djcelery project. I've used it in production for over a year now without
problems.


## How do I use this

You'll have to include the scheduler module in your project and modify the
models to work with your SQLAlchemy setup. The code in repo just uses temporary
in-memory SQLite database since I cannot assume anything.

Finally, set `CELERYBEAT_SCHEDULER` to
`yourproject.sqlalchemy_scheduler:DatabaseScheduler`.

Adding and removing tasks is done with manipulating the SQLAlchemy models.

```python
dse = model.DatabaseSchedulerEntry()
dse.name = 'Simple add task'
dse.task = 'yourproject.tasks.add'
dse.arguments = '[]'  # json string
dse.keyword_arguments = '{}'  # json string

# crontab defaults to run every minute
dse.crontab = model.CrontabSchedule()

dbsession.add(dse)
dbsession.commit()
```
