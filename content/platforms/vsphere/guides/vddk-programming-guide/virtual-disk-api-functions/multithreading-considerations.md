# Multithreading Considerations

In multithreaded programs, disk requests should be serialized by the client program. Disk handles are not bound to a thread and may be used across threads. You can open a disk in one thread and use its handle in another thread, provided you serialize disk access. Alternatively you can use a designated open-close thread, as shown in the workaround below.

## Multiple Threads and VixDiskLib

VDDK supports concurrent I/O to multiple virtual disks, with certain limitations:

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x25A0;</th>
      <th style="text-align:left">VixDiskLib_InitEx() or VixDiskLib_Init() should be called once per process,
        from the main thread.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">In the VixDiskLib_InitEx() or VixDiskLib_Init() function call, do not
        specify logging callbacks as NULL. This causes VixDiskLib to provide its
        default logging functions, which are not thread safe. If you are using
        VDDK in a multithreaded environment, you must provide your own thread-safe
        log functions.</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>When you call VixDiskLib_Open() and VixDiskLib_Close(), VDDK initializes
          and uninitializes a number of libraries, some of which do not work if called
          from multiple threads. For example, this fails:</p>
        <p>The workaround is to use one designated thread to do all opens and closes,
          and to have other worker threads doing reads and writes. This diagram shows
          concurrent reads on two separate disk handles. Concurrent reads on the
          same disk handles are not allowed.</p>
      </td>
    </tr>
  </tbody>
</table>