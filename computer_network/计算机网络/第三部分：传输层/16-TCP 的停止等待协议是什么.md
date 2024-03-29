停止等待协议是为了实现 TCP 可靠传输而提出的一种相对简单的协议，该协议指的是发送方每发完一组数据后，直到收到接收方的确认信号才继续发送下一组数据。我们通过四种情形来帮助理解停等协议是如何实现可靠传输的：

![无差错和超时重传](assets/无差错和超时重传.png)

① 无差错传输

如上述左图所示，A 发送分组 Msg 1，发完就暂停发送，直到收到接收方确认收到 Msg 1 的报文后，继续发送 Msg 2，以此类推，该情形是通信中的一种理想状态。

② 出现差错

如上述右图所示，发送方发送的报文出现差错导致接收方不能正确接收数据，出现差错的情况主要分为两种：

- 发送方发送的 Msg 1 在中途丢失了，接收方完全没收到数据。

- 接收方收到 Msg 1 后检测出现了差错，直接丢弃 Msg 1。

上面两种情形，接收方都不会回任何消息给发送方，此时就会触发超时传输机制，即发送方在等待一段时间后仍然没有收到接收方的确认，就认为刚才发送的数据丢失了，因此重传前面发送过的数据。

![确认丢失和确认重传](assets/确认丢失和确认重传.png)

③ 确认丢失

当接收方回应的 Msg 1 确认报文在传输过程中丢失，发送方无法接收到确认报文。于是发送方等待一段时间后重传 Msg 1，接收方将收到重复的 Msg1 数据包，此时接收方会丢弃掉这个重复报文并向发送方再次发送 Msg1 的确认报文。

④ 确认迟到

当接收方回应的 Msg 1 确认报文由于网络各种原因导致发送方没有及时收到，此时发送方在超时重传机制的作用下再次发送了 Msg 数据包，接收方此时进行和确认丢失情形下相同的动作（丢弃重复的数据包并再次发送 Msg 1 确认报文）。发送方此时收到了接收方的确认数据包，于是继续进行数据发送。过了一段时间后，发送方收到了迟到的 Msg 1 确认包会直接丢弃。

上述四种情形即停止等待协议中所出现的所有可能情况。

