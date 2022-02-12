Spark source for Flight enabled endpoints
=========================================

[![Build Status](https://travis-ci.org/rymurr/flight-spark-source.svg?branch=master)](https://travis-ci.org/rymurr/flight-spark-source)

This uses the new [Source V2 Interface](https://databricks.com/session/apache-spark-data-source-v2) to connect to 
[Apache Arrow Flight](https://www.dremio.com/understanding-apache-arrow-flight/) endpoints. It is a prototype of what is 
possible with Arrow Flight. The prototype has achieved 50x speed up compared to serial jdbc driver and scales with the
number of Flight endpoints/spark executors being run in parallel.

It currently supports:

* Columnar Batch reading
* Reading in parallel many flight endpoints as Spark partitions 
* filter and project pushdown

It currently lacks:

* support for all Spark/Arrow data types and filters
* write interface to use `DoPut` to write Spark dataframes back to an Arrow Flight endpoint
* leverage the transactional capabilities of the Spark Source V2 interface
* publish benchmark test


## Difference here and Spark

```
//Missing in spark's ArrowColumnVector - Start
  private static class DateMilliAccessor extends ArrowVectorAccessor {

    private final DateMilliVector accessor;
    private final double val = 1.0 / (24. * 60. * 60. * 1000.);

    DateMilliAccessor(DateMilliVector vector) {
      super(vector);
      this.accessor = vector;
    }

    @Override
    final int getInt(int rowId) {
      System.out.println(accessor.get(rowId) + " " + (accessor.get(rowId) * val) + " " + val);
      return (int) (accessor.get(rowId) * val);
    }
  }

  private static class TimestampMicroAccessor extends ArrowVectorAccessor {

    private final TimeStampVector accessor;

    TimestampMicroAccessor(TimeStampMicroVector vector) {
      super(vector);
      this.accessor = vector;
    }

    @Override
    final long getLong(int rowId) {
      return accessor.get(rowId);
    }
  }

  private static class TimestampMicroTZAccessor extends ArrowVectorAccessor {

    private final TimeStampVector accessor;

    TimestampMicroTZAccessor(TimeStampMicroTZVector vector) {
      super(vector);
      this.accessor = vector;
    }

    @Override
    final long getLong(int rowId) {
      return accessor.get(rowId);
    }
  }

  private static class TimestampMilliAccessor extends ArrowVectorAccessor {

    private final TimeStampVector accessor;

    TimestampMilliAccessor(TimeStampMilliVector vector) {
      super(vector);
      this.accessor = vector;
    }

    @Override
    final long getLong(int rowId) {
      return accessor.get(rowId) * 1000;
    }
  }
  //Missing in spark's ArrowColumnVector - End
```
