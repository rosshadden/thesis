# Appendices

## Tables

\pgfplotstableread{data/base.dat}\tableBase

\begin{table}[H]
	\begin{center}
		\pgfplotstabletypeset[
			column type=r,
			every head row/.style={
				before row=\hline,
				after row=\hline
			},
			every last row/.style={
				after row=\hline
			},
			every first column/.style={
				column type/.add={|}{}
			},
			every even column/.style={
				column type/.add={}{|}
			},
			every odd column/.style={
				column type/.add={}{|}
			},
		]\tableBase
	\end{center}

	\caption{Base noop connection average}
	\label{table:base}
\end{table}


\pgfplotstableread{data/local-file.dat}\tableFile

\begin{table}[H]
	\begin{center}
		\pgfplotstabletypeset[
			column type=r,
			every head row/.style={
				before row=\hline,
				after row=\hline
			},
			every last row/.style={
				after row=\hline
			},
			every first column/.style={
				column type/.add={|}{}
			},
			every even column/.style={
				column type/.add={}{|}
			},
			every odd column/.style={
				column type/.add={}{|}
			},
		]\tableFile
	\end{center}

	\caption{File result averages}
	\label{table:file}
\end{table}


\pgfplotstableread{data/local-database.dat}\tableDatabase

\begin{table}[H]
	\begin{center}
		\pgfplotstabletypeset[
			column type=r,
			every head row/.style={
				before row=\hline,
				after row=\hline
			},
			every last row/.style={
				after row=\hline
			},
			every first column/.style={
				column type/.add={|}{}
			},
			every even column/.style={
				column type/.add={}{|}
			},
			every odd column/.style={
				column type/.add={}{|}
			},
		]\tableDatabase
	\end{center}

	\caption{Database result averages}
	\label{table:database}
\end{table}


\pgfplotstableread{data/timeout.dat}\tableTimeout

\begin{table}[H]
	\begin{center}
		\pgfplotstabletypeset[
			column type=r,
			every head row/.style={
				before row=\hline,
				after row=\hline
			},
			every last row/.style={
				after row=\hline
			},
			every first column/.style={
				column type/.add={|}{}
			},
			every even column/.style={
				column type/.add={}{|}
			},
			every odd column/.style={
				column type/.add={}{|}
			},
		]\tableTimeout
	\end{center}

	\caption{Timeout result averages}
	\label{table:timeout}
\end{table}


\pgfplotstableread{data/series.dat}\tableSeries

\begin{table}[H]
	\begin{center}
		\pgfplotstabletypeset[
			column type=r,
			every head row/.style={
				before row=\hline,
				after row=\hline
			},
			every last row/.style={
				after row=\hline
			},
			every first column/.style={
				column type/.add={|}{}
			},
			every even column/.style={
				column type/.add={}{|}
			},
			every odd column/.style={
				column type/.add={}{|}
			},
		]\tableSeries
	\end{center}

	\caption{Series result averages}
	\label{table:series}
\end{table}


\pgfplotstableread{data/parallel.dat}\tableParallel

\begin{table}[H]
	\begin{center}
		\pgfplotstabletypeset[
			column type=r,
			every head row/.style={
				before row=\hline,
				after row=\hline
			},
			every last row/.style={
				after row=\hline
			},
			every first column/.style={
				column type/.add={|}{}
			},
			every even column/.style={
				column type/.add={}{|}
			},
			every odd column/.style={
				column type/.add={}{|}
			},
		]\tableParallel
	\end{center}

	\caption{Parallel result averages}
	\label{table:parallel}
\end{table}
